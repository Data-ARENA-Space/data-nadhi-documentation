# Data Nadhi Documentation

> **Direct. Transform. Deliver.**

Official documentation site for Data Nadhi - an open-source data pipeline platform that simplifies data ingestion, transformation, and delivery.

## 🌊 About Data Nadhi

Data Nadhi is a comprehensive data pipeline platform designed to handle the complete lifecycle of data processing:

- **Direct**: Streamlined data ingestion from application logs and various sources
- **Transform**: Powerful, flexible data transformation pipelines using Temporal workflows
- **Deliver**: Reliable data delivery to multiple destinations with built-in monitoring

This documentation provides comprehensive guides for understanding, setting up, and contributing to the Data Nadhi ecosystem.

## 📚 What's Inside

This documentation site is built with [Docusaurus](https://docusaurus.io/) and includes:

- **Architecture Documentation**: Detailed system design and component interactions
- **Setup Guides**: Step-by-step instructions for local development
- **API References**: Comprehensive API documentation for all services
- **Integration Guides**: How to integrate Data Nadhi into your applications

## 🚀 Quick Start

### Prerequisites

- Node.js >= 20.0
- npm or yarn

### Installation

```bash
npm install
```

### Development

Start the development server:

```bash
npm start
```

This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

### Build

Generate static content for production:

```bash
npm run build
```

This command generates static content into the `build` directory.

### Deployment

The documentation is deployed to Cloudflare Pages. The build output is automatically deployed on push to the main branch.

## 🏗️ Data Nadhi Repositories

Data Nadhi is composed of multiple repositories, each serving a specific purpose:

### Core Services

- **[data-nadhi-server](https://github.com/Data-ARENA-Space/data-nadhi-server)**  
  Main API server handling authentication, project management, and pipeline orchestration

- **[data-nadhi-internal-server](https://github.com/Data-ARENA-Space/data-nadhi-internal-server)**  
  Internal service for log ingestion and processing from client applications

- **[data-nadhi-temporal-worker](https://github.com/Data-ARENA-Space/data-nadhi-temporal-worker)**  
  Temporal worker service executing data transformation workflows and delivery jobs

### SDKs & Tools

- **[data-nadhi-sdk](https://github.com/Data-ARENA-Space/data-nadhi-sdk)**  
  JavaScript/TypeScript SDK for integrating Data Nadhi into applications

- **[data-nadhi-dev](https://github.com/Data-ARENA-Space/data-nadhi-dev)**  
  Development environment setup with Docker Compose, including all required infrastructure

### Documentation

- **[data-nadhi-documentation](https://github.com/Data-ARENA-Space/data-nadhi-documentation)** (this repo)  
  Official documentation site with architecture guides, setup instructions, and API references

## 🤝 Contributing

We welcome contributions! Here's how you can help:

1. **Documentation**: Found a typo or want to improve explanations? Edit the docs directly.
2. **Examples**: Add more examples and use cases to help others.
3. **Issues**: Report bugs or suggest improvements via GitHub Issues.

### Making Changes

1. Fork this repository
2. Create a branch: `git checkout -b feature/your-feature-name`
3. Make your changes
4. Test locally: `npm start`
5. Submit a pull request

## 📝 Documentation Structure

```
docs/
├── index.md                 # Introduction
├── architecture/            # System architecture
│   ├── index.md
│   ├── server/             # Main server architecture
│   ├── temporal/           # Temporal worker architecture
│   ├── sdk/                # SDK architecture
│   └── data/               # Data layer (MongoDB, Redis, MinIO)
└── setup/                   # Setup and installation guides
```

## 🔗 Links

- **Live Documentation**: [https://docs.datanad.com](https://docs.datanad.com)
- **GitHub Organization**: [Data-ARENA-Space](https://github.com/Data-ARENA-Space)
- **Main Website**: Coming soon

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 💬 Community

- **GitHub Discussions**: [Coming soon]
- **Discord**: [Coming soon]
- **Issues**: [GitHub Issues](https://github.com/Data-ARENA-Space/data-nadhi-documentation/issues)

---

Built with ❤️ by the Data ARENA Space team# data-nadhi-documentation
