# GitHub Actions Secrets Required

## Required for All Deployments
- `SSH_PRIVATE_KEY` - SSH private key for server access
- `SERVER_IP` - IP address of the target server
- `ANSIBLE_USER` - Username for SSH connection (e.g., ubuntu)

## Service-Specific Secrets

### Odoo
- `ODOO_MASTER_PASSWORD` - Master password for Odoo

### n8n
- `N8N_DOMAIN` - Domain name for n8n (e.g., n8n.yourdomain.com)
- `N8N_SSL_EMAIL` - Email for SSL certificate
- `N8N_POSTGRES_PASSWORD` - PostgreSQL password for n8n
- `N8N_BASIC_AUTH_USER` - Basic auth username (optional, defaults to admin)
- `N8N_BASIC_AUTH_PASSWORD` - Basic auth password

### KoboToolbox
- `KOBO_DOMAIN` - Main domain for KoboToolbox
- `KOBO_SUBDOMAIN` - Subdomain for KPI (usually 'kf')
- `KOBOCAT_SUBDOMAIN` - Subdomain for KoBoCAT (usually 'kc')
- `ENKETO_SUBDOMAIN` - Subdomain for Enketo (usually 'ee')
- `KOBO_POSTGRES_PASSWORD` - PostgreSQL password
- `KOBO_MONGO_PASSWORD` - MongoDB password
- `KOBO_REDIS_PASSWORD` - Redis password
