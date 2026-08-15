# 🌐 Palpo Matrix Stack

Complete Docker-based Palpo Matrix homeserver deployment with SSL certificate management for production and development environments.

## 🧩 Components

### 🔐 SSL Automation

#### [🔒 Let's Encrypt Manager](src/ssl-automation/letsencrypt-manager)

Automatic SSL certificate management from Let's Encrypt for production deployments. Provides seamless HTTPS integration for Docker containers using nginx-proxy and acme-companion.
[Learn more about Let's Encrypt Manager configuration](src/ssl-automation/letsencrypt-manager/README.md).

#### [🏠 Step CA Manager](src/ssl-automation/step-ca-manager)

Local domain stack with trusted self-signed certificates for virtual network deployments. Includes private CA management and local DNS resolution for development environments.
[Learn more about Step CA Manager configuration](src/ssl-automation/step-ca-manager/README.md).

## 🌐 Services

### 🌐 [Palpo Matrix Server](src/palpo/)

Modular Docker Compose configuration system for Palpo Matrix homeserver with support for multiple environments and TURN integration capabilities. Provides modern, Rust-based Matrix homeserver deployment with PostgreSQL backend and customizable configurations for development and production.
[Learn more about Palpo configuration](src/palpo/README.md).

### 🏠 [Homepage Gateway](src/homepage/)

Gateway service for providing external access to internal services on the primary domain. Essential for internal Palpo deployments where Matrix usernames should be tied to the primary domain (e.g., `@user:example.com`) instead of a subdomain.
[Learn more about Homepage Gateway configuration](src/homepage/README.md).

## 🚀 Quick Start

Each component has its own README with detailed setup instructions. Choose the certificate management solution that fits your deployment scenario.

### Basic Setup

1. **Choose SSL Management:**
   - Production: Use Let's Encrypt Manager
   - Development: Use Step CA Manager

2. **Deploy Palpo Matrix Server:**
   - Configure Palpo homeserver with desired environment and extensions

### Example Deployment

```bash
# 1. Build Palpo configurations
cd src/palpo/
sb build

# 2. Choose your deployment scenario
# For development with port forwarding
cd build/forwarding/base/

# For production with Let's Encrypt
cd build/letsencrypt/base/

# For production with Step CA and Step CA trust
cd build/step-ca/step-ca-trust/

# 3. Configure environment
cp .env.example .env
# Edit .env with your values

# 4. Deploy
docker compose up -d
```

## 🏗️ Architecture

### Standard Deployment (with subdomain)

```sh
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Matrix Client  │────│  Palpo Matrix   │────│   PostgreSQL    │
│ (Element/Cinny) │    │   Homeserver    │    │   (Database)    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │
         │                       │
         │             ┌─────────────────┐
         │             │  SSL Manager    │
         └─────────────│ (Let's Encrypt/ │
                       │  Step CA)       │
                       └─────────────────┘
```

### Internal Deployment (with Homepage Gateway on primary domain)

```sh
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Matrix Client  │────│    Homepage     │────│  Palpo Matrix   │
│ (Element/Cinny) │    │    Gateway      │    │   Homeserver    │
└─────────────────┘    │ (example.com)   │    │   (internal)    │
         │             └─────────────────┘    └─────────────────┘
         │                       │                       │
         │             ┌─────────────────┐    ┌─────────────────┐
         │             │  SSL Manager    │    │   PostgreSQL    │
         └─────────────│ (Let's Encrypt/ │    │   (Database)    │
                       │  Step CA)       │    └─────────────────┘
                       └─────────────────┘
```

**Note:** Internal deployment with Homepage Gateway allows Matrix usernames on the primary domain (e.g., `@user:example.com`) instead of a subdomain (e.g., `@user:matrix.example.com`).

## 📋 Requirements

- Docker & Docker Compose
- Domain name (for production deployments)
- Email address (for Let's Encrypt)
- Stackbuilder tool for configuration building

## 🔧 Configuration

All services use modular Docker Compose configurations with:

- **Base components**: Core service definitions
- **Environment components**: Development, production, SSL configurations
- **Extension components**: TURN integration, Step CA trust
- **Build system**: Automatic generation of deployment combinations

## 🌍 Deployment Scenarios

### Development Environment

```bash
# Palpo with port forwarding
cd src/palpo/build/forwarding/base/
docker compose up -d

# Palpo with port forwarding and TURN
cd src/palpo/build/forwarding/coturn/
docker compose up -d
```

### Production Environment

```bash
# Palpo with Let's Encrypt SSL
cd src/palpo/build/letsencrypt/base/
docker compose up -d

# Palpo with Let's Encrypt SSL and TURN
cd src/palpo/build/letsencrypt/coturn/
docker compose up -d

# Palpo with Step CA SSL and Step CA trust
cd src/palpo/build/step-ca/step-ca-trust/
docker compose up -d
```

### Internal Network Environment

Internal deployment is designed for scenarios where Palpo runs in an isolated network and external access is provided through Homepage Gateway on the primary domain. This allows Matrix usernames to be tied to the primary domain (e.g., `@user:example.com`) instead of a subdomain.

```bash
# 1. Deploy Palpo in internal network
cd src/palpo/build/internal/base/
docker compose up -d

# 2. Deploy Homepage Gateway on primary domain
cd src/homepage/build/letsencrypt/  # or step-ca
docker compose up -d

# With TURN support
cd src/palpo/build/internal/coturn/
docker compose up -d
```

**Note:** For external access in internal mode, Homepage Gateway must be deployed on your primary domain to route Matrix traffic to the internal Palpo instance.

### DevContainer Environment

```bash
# Palpo in DevContainer
cd src/palpo/build/devcontainer/base/
docker compose up -d

# Palpo in DevContainer with TURN
cd src/palpo/build/devcontainer/coturn/
docker compose up -d
```

## 🔐 Security Features

- **SSL/TLS Encryption**: Automatic certificate management
- **Network Isolation**: Docker network segmentation
- **Secret Management**: Environment-based configuration
- **Access Control**: Registration controls and federation settings
- **Step CA Trust**: Automatic certificate trust for internal services
- **Database Security**: PostgreSQL with configurable authentication

## 🎯 Palpo Features

- **Modern**: Rust-based Matrix homeserver with modern architecture
- **Performance**: PostgreSQL backend for optimal performance and reliability
- **Federation**: Full Matrix federation support
- **TURN**: Voice and video calling support with TURN server integration
- **SSL**: Full SSL/TLS support with Let's Encrypt and Step CA
- **Registration**: Configurable user registration
- **Flexible**: Multiple deployment configurations for different scenarios

## 🆘 Troubleshooting

### Common Issues

- **SSL Certificate Issues**: Check Let's Encrypt/Step CA configuration
- **Network Connectivity**: Ensure proper Docker network configuration
- **Federation Issues**: Check server name and SSL certificate configuration
- **Database Issues**: Verify PostgreSQL connectivity and permissions

### Logs

```bash
# Palpo logs
docker logs palpo

# Database logs
docker logs palpo-db

# SSL automation logs
docker logs nginx-proxy
docker logs letsencrypt-companion  # or step-ca-manager
```

## 📚 Documentation

- [Palpo Matrix Server Configuration](src/palpo/README.md)
- [SSL Automation](src/ssl-automation/)
- [Official Palpo Documentation](https://github.com/palpo-matrix-server/palpo)

## 🔗 Matrix Ecosystem

### Compatible Clients

- **Element**: Full-featured Matrix client
- **Cinny**: Modern Matrix client with clean UI
- **FluffyChat**: Cross-platform Matrix client
- **Nheko**: Desktop Matrix client

### Integration Services

- **Matrix Bridges**: Connect to Telegram, Discord, and other platforms
- **Maubot**: Matrix bot framework
- **Matrix Widgets**: Embedded applications in Matrix rooms

## 🌐 Federation

Palpo supports full Matrix federation, allowing communication with:

- **matrix.org**: The flagship Matrix homeserver
- **Other Palpo instances**: Modern Matrix homeservers
- **Synapse instances**: Reference Matrix homeserver implementation
- **Dendrite instances**: Next-generation Matrix homeserver
- **Conduit instances**: Lightweight Matrix homeservers

## 🔧 Advanced Configuration

### Custom Configurations

Each service supports extensive customization through:

- Environment variables
- Configuration files
- Docker Compose overrides
- Extension combinations

### Monitoring and Observability

- Container health checks
- Log aggregation
- Metrics collection (when integrated with monitoring stack)
- Performance monitoring

### Database Management

Palpo uses PostgreSQL for reliable data storage:

- Standard backup tools (pg_dump, pg_basebackup)
- Replication support
- Connection pooling
- ACID compliance

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test configurations
5. Submit a pull request

## 📄 License

This project is dual-licensed under:

- [Apache License 2.0](LICENSE-APACHE)
- [MIT License](LICENSE-MIT)

## 🔗 Related Projects

- [Matrix.org](https://matrix.org/) - Open network for secure, decentralized communication
- [Palpo](https://github.com/palpo-matrix-server/palpo) - Modern Matrix homeserver written in Rust
- [Element.io](https://element.io/) - Secure collaboration and messaging
- [Let's Encrypt](https://letsencrypt.org/) - Free SSL certificates
- [Smallstep](https://smallstep.com/) - Private certificate authority
- [PostgreSQL](https://www.postgresql.org/) - Advanced open source database
