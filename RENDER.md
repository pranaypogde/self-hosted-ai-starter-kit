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
   - Access: Via connection string in Render dashboard

2. **N8N Workflow Automation**
   - Plan: starter ($7/month)
   - Disk: 10GB persistent storage ($2.50/month)
   - Purpose: Orchestrates AI workflows
   - Access: Web interface at `https://n8n-[your-app-name].onrender.com`
   - Features:
     * Automatic PostgreSQL integration
     * Secure environment variables
     * Custom workflow storage

3. **Qdrant Vector Database**
   - Plan: starter ($7/month)
   - Disk: 10GB persistent storage ($2.50/month)
   - Purpose: Vector similarity search
   - Access: REST API at `https://qdrant-[your-app-name].onrender.com`
   - Features:
     * REST API interface
     * gRPC interface (if needed)
     * Persistent storage for vectors

## Verifying Services

### 1. N8N
- Access URL: `https://n8n-[your-app-name].onrender.com`
- You should see the N8N login page
- Log in with your credentials
- You can create and manage workflows through the web interface

### 2. Qdrant
- Access URL: `https://qdrant-[your-app-name].onrender.com`
- The root endpoint returns API information (this is normal):
  ```json
  {
    "title": "qdrant - vector search engine",
    "version": "1.12.4",
    "commit": "..."
  }
  ```
- To verify it's working, you can:
  1. Check the collections endpoint:
     ```bash
     curl https://qdrant-[your-app-name].onrender.com/collections
     ```
  2. Create a test collection:
     ```bash
     curl -X PUT https://qdrant-[your-app-name].onrender.com/collections/test_collection \
       -H 'Content-Type: application/json' \
       -d '{
         "vectors": {
           "size": 4,
           "distance": "Cosine"
         }
       }'
     ```

### 3. PostgreSQL
- Access: Via connection string in Render dashboard
- To verify:
  1. Get connection details from Render dashboard
  2. Use a PostgreSQL client to connect
  3. N8N will automatically use this database

## Required Files

1. **render.yaml** - Main blueprint configuration
2. **Dockerfile.n8n** - N8N service configuration:
```dockerfile
FROM n8nio/n8n:latest
EXPOSE 5678
```

3. **Dockerfile.qdrant** - Qdrant service configuration:
```dockerfile
FROM qdrant/qdrant:latest
EXPOSE 6333
EXPOSE 6334
VOLUME /qdrant/storage
```

## Deployment Steps

### 1. Prerequisites
- A GitHub account
- Your repository with the following files:
  * `render.yaml`
  * `Dockerfile.n8n`
  * `Dockerfile.qdrant`

### 2. Repository Setup
1. Create a new repository or clone the existing one:
```bash
git clone https://github.com/yourusername/your-repo.git
cd your-repo
```

2. Add the required files:
   - Copy the `render.yaml` configuration
   - Create `Dockerfile.n8n` and `Dockerfile.qdrant`
   - Commit and push the changes:
```bash
git add .
git commit -m "Add Render deployment configuration"
git push
```

### 3. Render Setup
1. Create an account on [Render](https://render.com)
2. Go to the Dashboard
3. Click "New +" and select "Blueprint"
4. Connect your GitHub repository
5. Render will automatically detect the `render.yaml` configuration

### 4. Environment Variables
During deployment, you'll need to set these secure variables:
- `N8N_ENCRYPTION_KEY`: Used for encrypting sensitive data
- `N8N_USER_MANAGEMENT_JWT_SECRET`: Used for user authentication

These should be strong, random values. Never reuse these values across different environments.

## Using the Services

### 1. N8N Workflows
1. Access N8N at `https://n8n-[your-app-name].onrender.com`
2. Create a new account on first login
3. Start creating workflows
4. Use Qdrant nodes to interact with your vector database

### 2. Qdrant Vector Database
1. Access via REST API at `https://qdrant-[your-app-name].onrender.com`
2. Use in your N8N workflows for vector operations
3. API Documentation available at `https://qdrant-[your-app-name].onrender.com/dashboard`

### 3. PostgreSQL Database
1. Used automatically by N8N
2. Access details available in Render dashboard
3. Can be used for additional storage needs

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

## Troubleshooting

### Common Issues
1. **Qdrant Returns Only API Information**
   - This is normal for the root endpoint
   - Use specific API endpoints for operations
   - Check API documentation at `/dashboard`

2. **N8N Login Issues**
   - Verify environment variables are set correctly
   - Check n8n logs in Render dashboard
   - Ensure database connection is working

3. **Database Connection Issues**
   - Verify credentials in Render dashboard
   - Check network access settings
   - Ensure SSL/TLS configuration is correct

### Support
- Render Documentation: [docs.render.com](https://docs.render.com)
- N8N Documentation: [docs.n8n.io](https://docs.n8n.io)
- Qdrant Documentation: [qdrant.tech/documentation](https://qdrant.tech/documentation)
