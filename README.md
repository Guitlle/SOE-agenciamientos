# DevOps Deployment Repository

This repository contains Ansible playbooks and GitHub Actions workflows for deploying Odoo 19, n8n, and KoboToolbox services.

## 📁 Repository Structure

```
.
├── ansible/                    # Ansible playbooks and inventory
│   ├── ansible.cfg            # Ansible configuration
│   ├── deploy.yml             # Main deployment playbook
│   ├── inventory/
│   │   └── hosts.ini          # Server inventory
│   └── playbooks/
│       ├── deploy_odoo.yml           # Odoo deployment
│       ├── deploy_n8n.yml            # n8n deployment
│       ├── deploy_kobotoolbox.yml    # KoboToolbox deployment
│       └── deploy_web_server.yml     # Nginx web server
├── .github/
│   └── workflows/
│       ├── deploy_playbook.yml       # Main deployment workflow
│       ├── deploy-service.yml        # Individual service deployment
│       ├── lint_ansible.yml          # Ansible linting
│       └── SECRETS.md                # Required secrets documentation
├── odoo-service/              # Odoo Docker Compose configuration
│   ├── docker-compose.yml
│   ├── .env
│   └── etcodoo/
├── n8n-service/               # n8n Docker Compose configuration
│   ├── docker-compose.yml
│   └── .env.example
└── kobotoolbox-service/       # KoboToolbox Docker Compose configuration
    ├── docker-compose.yml
    └── .env.example
```

## 🚀 Services Overview

### Odoo 19
- **Docker Image**: `odoo:19`
- **Database**: PostgreSQL 15
- **Ports**: 8069 (internal)
- **Features**: ERP/CRM system with full business management capabilities

### n8n
- **Docker Image**: `n8nio/n8n:latest`
- **Database**: PostgreSQL 15
- **Ports**: 5678 (n8n web interface), 80/443 (optional nginx)
- **Features**: Workflow automation tool with visual interface

### KoboToolbox
- **Docker Images**: Multiple (kpi, kobocat, enketo-express, nginx, postgres, mongo, redis)
- **Database**: PostgreSQL + PostGIS, MongoDB, Redis
- **Ports**: 80/443 (nginx)
- **Features**: Data collection and management platform for humanitarian and development work

## 🔧 Prerequisites

### Server Requirements
- Ubuntu 20.04 or later (Debian-based systems)
- Minimum 4GB RAM (8GB+ recommended for all three services)
- 50GB+ available disk space
- Docker and Docker Compose installed (will be installed by Ansible if not present)

### GitHub Repository Secrets
Add these secrets to your GitHub repository (Settings → Secrets and variables → Actions):

#### Required for All Deployments
- `SSH_PRIVATE_KEY` - SSH private key for server access
- `SERVER_IP` - IP address of your target server
- `ANSIBLE_USER` - Username for SSH connection (e.g., ubuntu)

#### Service-Specific Secrets

**Odoo**
- `ODOO_MASTER_PASSWORD` - Master password for Odoo database management

**n8n**
- `N8N_DOMAIN` - Domain name for n8n (e.g., n8n.yourdomain.com)
- `N8N_SSL_EMAIL` - Email for SSL certificate registration
- `N8N_POSTGRES_PASSWORD` - PostgreSQL password for n8n
- `N8N_BASIC_AUTH_USER` - Basic auth username (optional, defaults to admin)
- `N8N_BASIC_AUTH_PASSWORD` - Basic auth password

**KoboToolbox**
- `KOBO_DOMAIN` - Main domain for KoboToolbox (e.g., yourdomain.com)
- `KOBO_SUBDOMAIN` - Subdomain for KPI (usually 'kf')
- `KOBOCAT_SUBDOMAIN` - Subdomain for KoBoCAT (usually 'kc')
- `ENKETO_SUBDOMAIN` - Subdomain for Enketo (usually 'ee')

## 🚀 Deployment

### Automatic Deployment (Push to main)
Pushing to the `main` branch will automatically trigger deployment of all services:

```bash
git add .
git commit -m "Deploy updates"
git push origin main
```

### Manual Deployment (GitHub Actions)
You can manually deploy individual services via GitHub Actions:

1. Go to **Actions** tab in your GitHub repository
2. Select **"Deploy Individual Service"** workflow
3. Click **"Run workflow"**
4. Select the service to deploy:
   - `odoo` - Deploy only Odoo
   - `n8n` - Deploy only n8n
   - `kobotoolbox` - Deploy only KoboToolbox
5. Select the environment (production or staging)
6. Click **"Run workflow"**

### Local Deployment (Manual)
If you need to run deployments locally:

```bash
# Install Ansible
cd ansible
pip install ansible docker

# Install required collections
ansible-galaxy collection install community.docker

# Deploy all services
ansible-playbook deploy.yml -i inventory/hosts.ini --private-key ~/.ssh/id_rsa -u ubuntu

# Deploy specific service
ansible-playbook playbooks/deploy_n8n.yml -i inventory/hosts.ini --private-key ~/.ssh/id_rsa -u ubuntu
```

## ⚙️ Configuration

### Inventory Setup
Edit `ansible/inventory/hosts.ini` to add your server(s):

```ini
[production]
your-server-ip ansible_user=ubuntu

[staging]
your-staging-server-ip ansible_user=ubuntu
```

### Service Configuration

#### n8n
Copy `n8n-service/.env.example` to `n8n-service/.env` and customize:

```bash
cd n8n-service
cp .env.example .env
# Edit .env with your settings
```

#### KoboToolbox
Copy `kobotoolbox-service/.env.example` to `kobotoolbox-service/.env` and customize:

```bash
cd kobotoolbox-service
cp .env.example .env
# Edit .env with your settings
```

## 🔐 Security Considerations

1. **SSH Keys**: Use SSH key-based authentication, never passwords
2. **Environment Variables**: All sensitive data should be in GitHub Secrets
3. **SSL/TLS**: Configure SSL certificates for production (recommended to use nginx or traefik)
4. **Firewalls**: Ensure only necessary ports are open (80, 443, SSH)
5. **Backups**: Implement regular backup strategies for databases

## 📊 Monitoring & Maintenance

### View Logs
```bash
# SSH to your server
ssh ubuntu@your-server-ip

# View Odoo logs
docker logs -f odoo

# View n8n logs
docker logs -f n8n

# View KoboToolbox logs
cd /opt/kobotoolbox
docker-compose -f docker-compose.frontend.yml logs -f
```

### Backup
Regular backups should be configured for:
- PostgreSQL databases
- MongoDB data
- File uploads and media
- Configuration files

### Updates
To update services:

1. Update Docker images in compose files
2. Push changes to trigger deployment
3. Or manually trigger workflow from GitHub Actions

## 🛠️ Troubleshooting

### Common Issues

**1. Permission Denied**
Ensure SSH key is properly added to the server:
```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@your-server-ip
```

**2. Docker Not Installed**
Ansible will attempt to install Docker, but you may need to run:
```bash
sudo apt update
sudo apt install docker.io docker-compose-plugin
sudo usermod -aG docker $USER
```

**3. Port Conflicts**
Ensure no other services are using ports 80, 443, 5678, or 8069.

**4. Memory Issues**
If services fail to start, check available memory:
```bash
free -h
docker system prune -a  # Clean up unused images
```

## 📚 Resources

- [Odoo Documentation](https://www.odoo.com/documentation/19.0/)
- [n8n Documentation](https://docs.n8n.io/)
- [KoboToolbox Documentation](https://support.kobotoolbox.org/)
- [Ansible Documentation](https://docs.ansible.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Support

For issues and questions:
1. Check the troubleshooting section above
2. Review service-specific documentation
3. Open an issue in this repository
