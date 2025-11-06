# Implementation Details & AWS Skills

This document showcases specific AWS development skills through concrete code examples and architecture decisions from the Stock Screener project.

## Infrastructure as Code: AWS SAM

### Template Design Patterns

**Environment-Specific Configuration**:
```yaml
Parameters:
  Environment:
    Type: String
    AllowedValues: [dev, prod]

Conditions:
  IsProd: !Equals [!Ref Environment, prod]

Resources:
  DataCollectorFunction:
    Type: AWS::Serverless::Function
    Properties:
      MemorySize: !If [IsProd, 512, 256]  # Production-optimized memory
      Environment:
        Variables:
          S3_BUCKET_NAME: !Ref DataBucket
          ENVIRONMENT: !Ref Environment
```

**Deployment Workflow**:
```bash
# Build with dependency layers
sam build --use-container

# Deploy with parameter overrides
sam deploy --parameter-overrides Environment=prod --capabilities CAPABILITY_IAM

# Local testing during development
sam local invoke DataCollectorFunction --event events/test.json
```

### AWS CLI Operations

**Parameter Store for Secrets**:
```bash
# Store encrypted credentials
aws ssm put-parameter --name "/screener/alpaca/api_key" \
  --value "$API_KEY" --type "SecureString"

# Retrieve with decryption
aws ssm get-parameter --name "/screener/alpaca/api_key" --with-decryption
```

**Custom CloudWatch Metrics**:
```bash
# Publish business metrics
aws cloudwatch put-metric-data \
  --namespace "StockScreener/Business" \
  --metric-data MetricName=WorstDrawdown,Value=-25.5
```

## Lambda Function Development

### Function Design Patterns

**Separation of Concerns**:
- `data_collector_function`: Scheduled ETL processing (CloudWatch Events trigger)
- `telegram_bot_function`: Real-time user interaction (API Gateway webhook)
- `shared_layer`: Common dependencies (Pandas, requests, boto3)

**Error Handling with Graceful Degradation**:
```python
def process_stocks(symbols):
    results = []
    for symbol in symbols:
        try:
            data = fetch_stock_data(symbol)
            results.append(process_data(data))
        except APIRateLimitError:
            time.sleep(exponential_backoff())
            retry()
        except DataError as e:
            logger.error(f"Skipping {symbol}: {e}")
            continue  # Don't let one failure crash the batch
    return results
```

### Performance Optimization

**Cold Start Reduction** (8s → <2s):
- Extracted heavy dependencies (Pandas, NumPy) into Lambda layers
- Connection pooling: Reuse HTTP sessions across invocations
- Lazy imports for development-only modules

**Memory Tuning Through Testing**:
- Tested allocations from 256MB to 1024MB
- Found optimal: 512MB for data processing, 256MB for bot
- Higher memory = faster CPU, but diminishing returns after 512MB

### AWS Service Integration

**S3 for Cost-Optimized Storage**:
```python
# Presigned URLs for secure, temporary access
url = s3_client.generate_presigned_url(
    'get_object',
    Params={'Bucket': bucket, 'Key': 'daily_results.csv'},
    ExpiresIn=3600  # 1-hour expiration
)
```

**Parameter Store for Secrets**:
```python
# Runtime credential loading (never hardcoded)
def get_api_key():
    response = ssm.get_parameter(
        Name='/screener/alpaca/api_key',
        WithDecryption=True
    )
    return response['Parameter']['Value']
```

## Monitoring & Observability

### Structured Logging
```python
import json

# JSON-formatted logs for easy parsing
def log_event(level, message, **context):
    print(json.dumps({
        'timestamp': datetime.utcnow().isoformat(),
        'level': level,
        'message': message,
        **context
    }))
```

### Custom CloudWatch Metrics
```python
# Business-specific metrics
cloudwatch.put_metric_data(
    Namespace='StockScreener/Business',
    MetricData=[{
        'MetricName': 'WorstDailyDrawdown',
        'Value': worst_drawdown,
        'Unit': 'Percent'
    }]
)
```

## Security Implementation

### Least-Privilege IAM Roles
```yaml
DataCollectorRole:
  Type: AWS::IAM::Role
  Properties:
    Policies:
      - PolicyDocument:
          Statement:
            - Effect: Allow
              Action: [s3:GetObject, s3:PutObject]
              Resource: !Sub "${S3Bucket}/*"
            - Effect: Allow
              Action: ssm:GetParameter
              Resource: !Sub "arn:aws:ssm:*:*:parameter/screener/*"
```

### Secrets Hierarchy
```
/screener/alpaca/api_key       # Alpaca credentials
/screener/alpaca/secret_key
/screener/telegram/bot_token   # Telegram bot token
```

## API Integration

### Rate Limiting for Alpaca API
```python
class RateLimiter:
    def __init__(self, max_requests=200, time_window=60):
        self.max_requests = max_requests
        self.time_window = time_window
        self.requests = deque()

    def wait_if_needed(self):
        now = time.time()
        # Remove old requests
        while self.requests and self.requests[0] < now - self.time_window:
            self.requests.popleft()
        # Wait if at limit
        if len(self.requests) >= self.max_requests:
            time.sleep(self.time_window - (now - self.requests[0]))
        self.requests.append(now)
```

### Retry Logic with Exponential Backoff
```python
def api_call_with_retry(url, max_retries=3):
    for attempt in range(max_retries):
        try:
            response = requests.get(url, timeout=10)
            response.raise_for_status()
            return response.json()
        except requests.RequestException:
            if attempt == max_retries - 1:
                raise
            time.sleep(2 ** attempt)  # 1s, 2s, 4s
```

## Development Approach

### Iterative Development & Testing

**Local Development with SAM**:
```bash
# Test functions locally before deploying
sam local invoke DataCollectorFunction --event events/test.json
sam local start-api  # Test API Gateway integration
```

**Incremental Deployment**:
- Developed features iteratively with frequent deployments to dev environment
- Monitored CloudWatch Logs for errors and performance issues
- Made adjustments based on real-world behavior and edge cases
- Promoted to production after validating in dev

**Real-World Validation**:
- Tested error handling by observing actual API failures and missing data
- Optimized memory allocation by measuring execution times across different settings
- Refined rate limiting based on actual API responses
- Improved logging based on debugging needs during troubleshooting

## Key Technical Learnings

### Architecture Decisions
- **S3 vs Database**: Analyzed access patterns—chose S3 CSV for append-only workload (90% cost savings)
- **Parameter Store vs Secrets Manager**: Parameter Store met security needs with lower complexity
- **Lambda Layers**: Shared dependencies reduced package size 60%, improved cold starts

### Performance Insights
- **Memory Allocation**: Empirical testing found 512MB optimal for data processing, 256MB for bot
- **Cold Starts**: Lambda layers and connection pooling reduced from 8s to <2s
- **Error Handling**: Graceful degradation more valuable than perfect execution

### Cost Optimization
- Serverless pay-per-use eliminated idle costs
- CSV storage vs. databases: Right tool for the access pattern
- CloudWatch free tier adequate for monitoring needs

---

*This implementation demonstrates production AWS development: infrastructure as code, security best practices, cost optimization, performance tuning, and operational monitoring.*