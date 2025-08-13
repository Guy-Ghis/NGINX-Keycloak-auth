# Keycloak + OAuth2 Proxy + Nginx Authentication Stack

A production-ready authentication infrastructure using Keycloak as the identity provider, OAuth2 Proxy for authentication middleware, and Nginx for reverse proxy with authentication enforcement.

## 🏗️ Architecture

```plaintext
┌─────────────┐    ┌──────────────┐    ┌─────────────┐    ┌─────────────┐
│    Client   │───▶│     Nginx    │───▶│   OAuth2    │───▶│  Keycloak   │
│             │    │  (Port 8081) │    │   Proxy     │    │ (Port 8080) │
└─────────────┘    └──────────────┘    └─────────────┘    └─────────────┘
                           │                    │
                           ▼                    ▼
                    ┌─────────────┐     ┌─────────────┐
                    │   httpbin   │     │   Protected │
                    │ (Port 8000) │     │   Backend   │
                    └─────────────┘     └─────────────┘
```

## 🚀 Features

- **Keycloak Identity Provider**: Centralized authentication and authorization
- **OAuth2 Proxy**: Handles OIDC authentication flow and session management
- **Nginx Reverse Proxy**: Routes traffic and enforces authentication via `auth_request`
- **Health Checks**: Built-in health monitoring for all services
- **Docker Compose**: Easy deployment and management
- **Pre-configured Realm**: Includes a sample `blog-realm` configuration

## 📋 Prerequisites

- Docker Engine 20.10+
- Docker Compose 2.0+
- At least 2GB RAM available
- Ports 8000, 8080, 8081, and 4180 available

## 🛠️ Quick Start

1. **Clone and navigate to the project:**

   ```bash
   cd nginx+keycloak-auth
   ```

2. **Start all services:**

   ```bash
   docker compose up -d
   ```

3. **Wait for services to be healthy:**

   ```bash
   docker compose ps
   ```

4. **Access the services:**
   - **Keycloak Admin Console**: http://localhost:8080 (admin/admin)
   - **Protected Application**: http://localhost:8081
   - **OAuth2 Proxy**: http://localhost:4180
   - **httpbin (Backend)**: http://localhost:8000

## 🔧 Configuration

### Keycloak

- **Port**: 8080
- **Admin**: admin/admin
- **Database**: In-memory H2 (dev mode)
- **Realm**: Automatically imports `blog-realm` from `keycloak/realm-export.json`

### OAuth2 Proxy

- **Port**: 4180
- **Provider**: OIDC (Keycloak)
- **Client**: `blog-client`
- **Redirect**: http://localhost:8081/oauth2/callback

### Nginx

- **Port**: 8081
- **Authentication**: Enforced via OAuth2 Proxy
- **Backend**: Proxies to httpbin service
- **Health Check**: Available at `/health`

## 🔐 Authentication Flow

1. **Unauthenticated Request**: User accesses protected resource
2. **Auth Request**: Nginx forwards to OAuth2 Proxy for validation
3. **OIDC Flow**: OAuth2 Proxy redirects to Keycloak for login
4. **User Authentication**: User logs in via Keycloak
5. **Token Validation**: OAuth2 Proxy validates tokens with Keycloak
6. **Access Granted**: User can access protected resources

## 📁 Project Structure

```plaintext
nginx+keycloak-auth
├── docker-compose.yml          # Service orchestration
├── keycloak/
│   └── realm-export.json      # Keycloak realm configuration
├── nginx/
│   └── nginx.conf             # Nginx configuration with auth
└── README.md                  # This file
```

## 🧪 Testing

### Health Checks

```bash
# Check all service health
docker compose ps

# View service logs
docker compose logs [service-name]

# Test endpoints
curl http://localhost:8081/health
curl http://localhost:8000/anything
```

### Authentication Test

1. Access http://localhost:8081
2. You'll be redirected to Keycloak login
3. Use credentials from your realm
4. After authentication, you'll access the protected resource

## 🔒 Security Considerations

- **Development Mode**: This setup uses Keycloak dev mode - not for production
- **HTTP Only**: Configured for local development (no HTTPS)
- **Default Credentials**: Change admin passwords in production
- **Cookie Security**: OAuth2 Proxy cookies set to non-secure for local dev

## 🚀 Production Deployment

1. **Enable HTTPS**: Configure SSL certificates
2. **Database**: Use PostgreSQL or MySQL instead of H2
3. **Secrets**: Use environment variables or Docker secrets
4. **Monitoring**: Add logging and metrics collection
5. **Scaling**: Consider container orchestration (Kubernetes)

## 🐛 Troubleshooting

### Common Issues

**Services not starting:**

```bash
# Check service status
docker compose ps

# View logs
docker compose logs [service-name]

# Restart services
docker compose restart
```

**Authentication failures:**

- Verify Keycloak realm is properly configured
- Check OAuth2 Proxy environment variables
- Ensure network connectivity between services

**Port conflicts:**

- Verify ports 8000, 8080, 8081, and 4180 are available
- Check for existing services using these ports

## 📚 Additional Resources

- [Keycloak Documentation](https://www.keycloak.org/documentation)
- [OAuth2 Proxy Documentation](https://oauth2-proxy.github.io/oauth2-proxy/)
- [Nginx auth_request Module](http://nginx.org/en/docs/http/ngx_http_auth_request_module.html)

## 📄 License

This project is provided as-is for educational and development purposes.

---

**Note**: This is a development setup. For production use, ensure proper security hardening, HTTPS configuration, and database persistence.
