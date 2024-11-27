# Render Deployment Guide

This guide explains how to deploy the self-hosted AI starter kit on Render.com.

## Estimated Monthly Costs

Based on Render's pricing:
- PostgreSQL (Basic-1gb): $23.50/month
- N8N (Starter): $7/month
- Qdrant (Starter): $7/month
- Disk Storage (20GB total): $5/month
  * N8N: 10GB ($2.50)
  * Qdrant: 10GB ($2.50)

Total estimated cost: $42.50 per month

## Services Overview

The deployment consists of three main services:

1. **PostgreSQL Database**
   - Plan: basic-1gb ($23.50/month)
   - Version: PostgreSQL 16
   - Disk: 15GB included
   - Purpose: Stores n8n workflows and data

2. **N8N Workflow Automation**
   - Plan: starter ($7/month)
   - Disk: 10GB persistent storage ($2.50/month)
   - Purpose: Orchestrates AI workflows
   - Features:
     * Automatic PostgreSQL integration
     * Secure environment variables
     * Custom workflow storage

3. **Qdrant Vector Database**
   - Plan: starter ($7/month)
   - Disk: 10GB persistent storage ($2.50/month)
   - Purpose: Vector similarity search
   - Custom Dockerfile configuration

## Deployment Steps

### 1. Prerequisites
- A GitHub account
- Your repository with the following files:
  * `render.yaml` (Blueprint configuration)
  * `Dockerfile.qdrant` (Qdrant configuration)

### 2. Render Setup
1. Create an account on [Render](https://render.com)
2. Go to the Dashboard
3. Click "New +" and select "Blueprint"
4. Connect your GitHub repository
5. Render will automatically detect the `render.yaml` configuration

### 3. Environment Variables
During deployment, you'll need to set these secure variables:
- `N8N_ENCRYPTION_KEY`: Used for encrypting sensitive data
- `N8N_USER_MANAGEMENT_JWT_SECRET`: Used for user authentication

These should be strong, random values. Never reuse these values across different environments.

### 4. Service URLs
After deployment, your services will be available at:
- N8N: `https://n8n-[your-app-name].onrender.com`
- Qdrant: `https://qdrant-[your-app-name].onrender.com`
- PostgreSQL: Connection details available in Render dashboard

## Configuration Details

### PostgreSQL Configuration
```yaml
databases:
  - name: n8n-db
    databaseName: n8n_production
    user: n8n_user
    plan: basic-1gb
    postgresMajorVersion: "16"
    diskSizeGB: 15
```

### N8N Configuration
```yaml
services:
  - type: web
    name: n8n
    env: docker
    plan: starter
    disk:
      name: n8n-data
      mountPath: /home/node/.n8n
      sizeGB: 10
```

### Qdrant Configuration
```yaml
services:
  - type: web
    name: qdrant
    env: docker
    plan: starter
    dockerfilePath: ./Dockerfile.qdrant
    disk:
      name: qdrant-data
      mountPath: /qdrant/storage
      sizeGB: 10
```

## Production Considerations

### Scaling
- Monitor resource usage and upgrade plans as needed
- Consider upgrading PostgreSQL to basic-4gb for higher workloads
- Adjust disk sizes based on data growth

### Security
- Database is accessible externally but protected by credentials
- Use SSL/TLS for all database connections
- Regularly rotate security keys and credentials
- Monitor access logs for suspicious activity

### Backup
- Regularly backup n8n workflows
- Set up PostgreSQL backups
- Consider implementing disaster recovery procedures

### Monitoring
- Set up monitoring for all services
- Monitor disk usage and performance metrics
- Set up alerts for critical service metrics

## Cost Optimization
- Start with starter plans for N8N and Qdrant
- Monitor usage and adjust resources as needed
- Consider disk size requirements carefully
- Set up billing alerts to monitor costs

## Troubleshooting

### Common Issues
1. **Database Connection Issues**
   - Verify database credentials
   - Check network access settings
   - Ensure SSL/TLS configuration is correct

2. **N8N Startup Problems**
   - Verify environment variables are set correctly
   - Check n8n logs in Render dashboard
   - Verify disk permissions

3. **Qdrant Connection Issues**
   - Check Qdrant logs in Render dashboard
   - Verify disk mount configuration
   - Check network connectivity

### Support
- Render Documentation: [docs.render.com](https://docs.render.com)
- N8N Documentation: [docs.n8n.io](https://docs.n8n.io)
- Qdrant Documentation: [qdrant.tech/documentation](https://qdrant.tech/documentation)
