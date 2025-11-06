# AWS Serverless Stock Screener

## Project Overview

A production serverless application demonstrating end-to-end AWS development through automated stock market analysis. Built as a technical learning project to implement a systematic trading entry/exit criteria while solving real infrastructure challenges: cost optimization, security, monitoring, and scalable architecture.

## 🎯 Technical Achievements

- **90% Cost Reduction**: Smart architecture choice (CSV storage vs. databases) reduced monthly costs from $50+ to $0.30
- **Production Reliability**: 99%+ uptime with comprehensive error handling and graceful degradation
- **Performance Optimization**: Sub-2-second bot response times through memory tuning and Lambda layer optimization
- **Scalable Processing**: Daily analysis of 200+ stocks with rate-limit-aware batch processing
- **Security Implementation**: Zero hardcoded secrets, least-privilege IAM, encrypted Parameter Store
- **Full Automation**: Eliminated 2-3 hours of daily manual analysis through event-driven architecture

## 🛠 Technology Stack

**AWS Core**: Lambda (event-driven processing) • API Gateway (webhook interface) • S3 (CSV storage) • Parameter Store (encrypted secrets) • CloudWatch (custom metrics, alerting) • IAM (least-privilege policies)

**Infrastructure**: AWS SAM (Infrastructure as Code) • AWS CLI (automation scripting) • CloudFormation (stack deployment)

**Development**: Python 3.11 • Pandas (data processing) • Alpaca Markets API (market data) • Telegram Bot API (user interface)

## 🏗 Architecture Highlights

**Event-Driven Serverless**: CloudWatch Events trigger scheduled data collection; API Gateway handles real-time Telegram webhooks. Separate Lambda functions for data processing vs. user interaction with shared layers for common dependencies.

**Cost-Optimized Storage**: S3 CSV storage chosen over databases after analyzing append-only access pattern—90% cost savings while maintaining full functionality.

**Production Security**: Parameter Store for encrypted secrets • Least-privilege IAM roles per function • Runtime credential loading • Comprehensive CloudWatch logging for audit trail.

**Reliable Processing**: Graceful degradation (individual failures don't crash batch) • Rate limiter for API compliance • Exponential backoff for retries • Custom CloudWatch metrics for business monitoring.

## 📊 Project Concept: Automated Trading Strategy

**Note**: This project was built as a technical learning exercise to implement systematic trading entry/exit criteria. The investment approach itself is secondary to the AWS implementation skills demonstrated.

**System Logic**: Identifies stocks with significant drawdowns from recent peaks (contrarian value approach), ranks by 180-day performance decline, and provides automated alerts via Telegram. This created real-world requirements for data processing, scheduling, monitoring, and user notification—excellent drivers for learning production AWS development.

**Future Expansion Potential**: The architecture could support automated trade execution, but intentionally stopped at analysis/alerts to focus on core AWS infrastructure skills.

## 🚀 System Features

- **Scheduled Processing**: Daily automated screening of 200+ Russell 1000 stocks via CloudWatch Events
- **Telegram Bot Interface**: Real-time query responses (<2 seconds) for current opportunities and portfolio status
- **Data Management**: Secure S3 storage with presigned URL downloads for historical analysis
- **Monitoring Dashboard**: Custom CloudWatch metrics tracking system health and business metrics
- **Graceful Error Handling**: Individual stock failures don't prevent batch completion; comprehensive logging for debugging

## 📁 Repository Structure

```
aws-stock-screener/
├── README.md                 # This file - project overview
├── ARCHITECTURE.md           # Technical deep dive and design decisions
├── BUSINESS-IMPACT.md        # Performance metrics and business value
├── IMPLEMENTATION.md         # Development process and AWS skills
├── lessons-learned.md        # Insights and future improvements
├── architecture-diagrams/    # Visual system representations
└── aws-console-screenshots/  # AWS implementation evidence
```

## 🔗 Quick Navigation

- **[Technical Architecture](./ARCHITECTURE.md)** - Deep dive into system design and AWS integration
- **[Business Impact](./BUSINESS-IMPACT.md)** - ROI analysis and performance metrics  
- **[Implementation Guide](./IMPLEMENTATION.md)** - Development process and AWS skills demonstrated
- **[Lessons Learned](./lessons-learned.md)** - Key insights and future improvements

## 🎖 AWS Skills Demonstrated

**Serverless Development**: Lambda function optimization • API Gateway webhook integration • Event-driven architecture • Microservices decomposition

**Infrastructure as Code**: AWS SAM template design • Multi-environment deployment • CloudFormation stack management • AWS CLI automation scripting

**Security Best Practices**: Parameter Store secrets management • Least-privilege IAM policy design • Runtime credential loading • Comprehensive audit logging

**Production Operations**: CloudWatch custom metrics and dashboards • Structured logging • Performance optimization (memory tuning, cold start reduction) • Cost optimization strategies

**API Integration**: Rate limit management • Error handling with exponential backoff • Graceful degradation • External service integration (Alpaca, Telegram)

---

*This project showcases production-ready AWS serverless development skills with measurable business impact and comprehensive technical implementation.*