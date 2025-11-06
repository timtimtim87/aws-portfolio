# AWS Serverless Stock Screener

## Project Overview

A production-grade serverless application that implements an automated stock screening system using a contrarian value investing strategy. The system monitors Russell 1000 stocks, identifies beaten-down opportunities, and provides real-time insights through a Telegram bot interface.

## Architecture Highlights

**Serverless-First Design**: Built entirely on AWS serverless services to minimize operational overhead and cost while maintaining high availability and scalability.

**Event-Driven Processing**: Uses scheduled Lambda functions for daily data collection and API Gateway for real-time bot interactions.

**Cost-Optimized Storage**: Leverages S3 CSV storage instead of expensive databases, reducing monthly storage costs to approximately $0.30.

## Technical Skills Demonstrated

### AWS Services Mastery
- **Lambda**: Designed multi-function architecture with shared layers and optimized memory allocation
- **API Gateway**: RESTful endpoints for webhook integration with external services
- **S3**: Cost-effective data storage with presigned URLs for secure file access
- **Parameter Store**: Secure credential management with encrypted parameter storage
- **CloudWatch**: Comprehensive logging and monitoring with custom metrics and alarms
- **IAM**: Least-privilege security policies for service-to-service communication

### DevOps & Infrastructure as Code
- **AWS SAM**: Complete infrastructure definition using SAM templates for reproducible deployments
- **CloudFormation**: Advanced template features including parameters, outputs, and cross-stack references
- **CI/CD Pipeline**: Automated build and deployment processes using `sam build` and `sam deploy`

### API Integrations
- **Financial Data APIs**: Integration with Alpaca Markets for real-time and historical market data
- **Telegram Bot API**: Webhook-based messaging system for instant notifications and commands
- **RESTful Design**: Proper HTTP status codes, error handling, and response formatting

### Data Engineering
- **ETL Processes**: Daily extraction, transformation, and loading of financial data
- **Data Quality**: Error handling, data validation, and graceful degradation
- **Performance Optimization**: Efficient batch processing of 200+ stock symbols

## Business Value Delivered

### Investment Strategy Automation
- **Systematic Screening**: Automated identification of oversold stocks using 180-day peak-to-current drawdown analysis
- **Risk Management**: Built-in profit-taking rules based on portfolio-wide performance metrics
- **Real-time Monitoring**: Instant access to portfolio status and market opportunities

### Operational Efficiency
- **24/7 Availability**: Serverless architecture ensures continuous operation without manual intervention
- **Scalability**: Auto-scaling capabilities handle varying market data loads
- **Cost Control**: Monthly operational costs under $5 for complete system

## Key Technical Decisions

### Why Serverless?
Chose serverless architecture to eliminate server management overhead and provide automatic scaling. This decision reduced operational complexity while maintaining high availability.

### Why CSV Storage Over Databases?
Selected S3 CSV storage over traditional databases to minimize costs for this read-heavy, append-only use case. This architectural choice reduced storage costs by 90% compared to RDS solutions.

### Why Telegram Integration?
Implemented Telegram bot for instant mobile access to data and alerts. This provides immediate notification capabilities without building a custom mobile application.

## Implementation Challenges Solved

### Financial Data Processing
- Handled API rate limits and market data inconsistencies
- Implemented robust error handling for missing or delayed data
- Designed efficient batch processing for large datasets

### Security & Compliance
- Secured API credentials using AWS Parameter Store with encryption
- Implemented proper IAM roles with minimal required permissions
- Ensured no sensitive data exposure in logs or outputs

### Performance Optimization
- Optimized Lambda function memory allocation and timeout settings
- Implemented shared layers to reduce deployment package size
- Used connection pooling and efficient data structures for processing

## Monitoring & Reliability

### Observability
- CloudWatch dashboards for system health monitoring
- Custom metrics for business KPIs (drawdown trends, portfolio performance)
- Automated alerting for system failures and cost thresholds

### Error Handling
- Graceful degradation when external APIs are unavailable
- Retry logic with exponential backoff for transient failures
- Comprehensive logging for debugging and audit trails

## Future Enhancements

### Planned Improvements
- Machine learning models for enhanced stock selection
- Multi-asset class support (bonds, ETFs, cryptocurrencies)
- Advanced portfolio optimization algorithms
- Web dashboard for enhanced visualization

### Scalability Considerations
- Design supports expansion to multiple markets and time zones
- Architecture can handle increased data frequency (hourly vs. daily)
- Modular design allows for easy feature additions

## Project Impact

This project demonstrates end-to-end AWS expertise, from infrastructure design to production deployment. It showcases ability to:
- Design cost-effective, scalable cloud architectures
- Integrate multiple third-party APIs securely
- Implement proper DevOps practices with Infrastructure as Code
- Build production-ready applications with monitoring and alerting
- Balance technical complexity with business requirements

## Technologies Used

**Cloud Platform**: AWS (Lambda, API Gateway, S3, Parameter Store, CloudWatch)  
**Infrastructure**: AWS SAM, CloudFormation, AWS CLI  
**Programming**: Python 3.11, Boto3 SDK  
**APIs**: Alpaca Markets, Telegram Bot API  
**Data Processing**: Pandas, NumPy  
**Monitoring**: CloudWatch Logs, Metrics, and Alarms  
**Security**: IAM, Parameter Store encryption, VPC (optional)  

---

*This project represents a comprehensive demonstration of modern serverless architecture principles applied to financial technology challenges.*