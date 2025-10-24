# Data Nadhi Documentation

> **Direct. Transform. Deliver.**

Docs for Data Nadhi - an open-source data pipeline platform that handles your data from logs to destination.

## 🌊 About Data Nadhi

Data Nadhi handles the complete pipeline flow starting from your application logs all the way to any destination you want:

- **Direct**: Get data from your application logs and various sources into the system
- **Transform**: Apply flexible transformations to your data using Temporal workflows
- **Deliver**: Send the processed data to multiple destinations reliably, with built-in monitoring

These docs will help you understand how Data Nadhi works, get it set up, and contribute if you want to.

## 📚 What's Inside

Built with [Docusaurus](https://docusaurus.io/), this site includes:

- **Architecture Documentation**: Detailed breakdown of how the system is designed and how components interact with each other
- **Setup Guides**: Step-by-step instructions to get Data Nadhi running locally
- **API References**: Complete API documentation for the services
- **Integration Guides**: How to integrate Data Nadhi into your existing applications (Once everything is up in docker)

## 🚀 Quick Start

### Prerequisites

- Node.js >= 20.0
- npm or yarn

### Installation

```bash
npm install
```

### Development

Start the dev server:

```bash
npm start
```

This starts a local server and opens a browser window. Most changes will show up live without needing to restart.

### Build

Build the static site for production:

```bash
npm run build
```

This generates all the static content and puts it in the `build` directory.

### Deployment

The docs are hosted on Cloudflare Pages. Any push to the main branch automatically triggers a deployment.

## 🏗️ Data Nadhi Repositories

Data Nadhi is split across multiple repos, each handling a specific part:

### Core Services

- **[data-nadhi-server](https://github.com/Data-ARENA-Space/data-nadhi-server)**  
  The API server that handles authentication for the request from SDK and pushing to temporal

- **[data-nadhi-internal-server](https://github.com/Data-ARENA-Space/data-nadhi-internal-server)**  
  Internal service that temporarily replaces the UI that will help with creating pipeline nodes and other entities

- **[data-nadhi-temporal-worker](https://github.com/Data-ARENA-Space/data-nadhi-temporal-worker)**  
  This is a set of 3 workers, that are responsible for the code data pipeline traversal with the log data

### SDKs & Tools

- **[data-nadhi-sdk](https://github.com/Data-ARENA-Space/data-nadhi-sdk)**  
  Python SDK for integrating Data Nadhi into your applications - The logging SDK

- **[data-nadhi-dev](https://github.com/Data-ARENA-Space/data-nadhi-dev)**  
  Development environment setup with Docker Compose, includes all the infrastructure you need in your local

### Documentation

- **[data-nadhi-documentation](https://github.com/Data-ARENA-Space/data-nadhi-documentation)** (this repo)  
  This documentation site with architecture guides, setup instructions, and API references

## 🤝 Contributing

Contributions are always welcome! Here's how you can help:

1. **Documentation**: Found a typo or something that could be explained better? Go ahead and edit it.
2. **Examples**: Add more examples and use cases that could help others.
3. **Issues**: Report bugs or suggest improvements through GitHub Issues.

### Making Changes

1. Fork this repo
2. Create a branch: `git checkout -b feature/your-feature-name`
3. Make your changes
4. Test locally with `npm start`
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

- **Live Documentation**: [https://docs.datanadhi.com](https://docs.datanadhi.com)
- **GitHub Organization**: [Data-ARENA-Space](https://github.com/Data-ARENA-Space)
- **Main Website**: [https://datanadhi.com](https://datanadhi.com)

## 📄 License

This project is open source and available under the [GNU Affero General Public License v3.0](LICENSE).

## 💬 Community

- **GitHub Discussions**: [Coming soon]
- **Discord**: [Coming soon]
- **Issues**: [GitHub Issues](https://github.com/Data-ARENA-Space/data-nadhi-documentation/issues)
