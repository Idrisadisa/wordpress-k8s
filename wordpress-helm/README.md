# WordPress Helm Chart

A production-ready Helm chart for deploying WordPress with MySQL on Kubernetes.

## Features

- **Production Ready**: Includes health checks, resource limits, and security contexts
- **High Availability**: Support for horizontal pod autoscaling and pod disruption budgets
- **Security**: Network policies, non-root containers, and secret management
- **Persistence**: Configurable persistent storage for WordPress and MySQL data
- **Ingress**: Built-in ingress support with TLS termination
- **Monitoring**: Ready for Prometheus monitoring with proper labels
- **Flexibility**: Extensive configuration options via values.yaml

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure
- Ingress controller (if ingress is enabled)

## Installation

### Quick Start

```bash
helm install my-wordpress ./wordpress-helm
```

### Custom Installation

```bash
helm install my-wordpress ./wordpress-helm \
  --set wordpress.auth.username=admin \
  --set wordpress.auth.password=mypassword \
  --set ingress.hosts[0].host=myblog.example.com
```

### Using Custom Values

```bash
helm install my-wordpress ./wordpress-helm -f my-values.yaml
```

## Configuration

### WordPress Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `wordpress.replicaCount` | Number of WordPress replicas | `1` |
| `wordpress.image.repository` | WordPress image repository | `wordpress` |
| `wordpress.image.tag` | WordPress image tag | `6.4.2-apache` |
| `wordpress.auth.username` | WordPress admin username | `admin` |
| `wordpress.auth.password` | WordPress admin password | `""` (auto-generated) |
| `wordpress.auth.email` | WordPress admin email | `admin@example.com` |
| `wordpress.persistence.enabled` | Enable persistence | `true` |
| `wordpress.persistence.size` | Storage size | `10Gi` |

### MySQL Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `mysql.enabled` | Enable MySQL | `true` |
| `mysql.auth.database` | MySQL database name | `wordpress` |
| `mysql.auth.username` | MySQL username | `wordpress` |
| `mysql.auth.password` | MySQL password | `""` (auto-generated) |
| `mysql.persistence.enabled` | Enable persistence | `true` |
| `mysql.persistence.size` | Storage size | `8Gi` |

### Ingress Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `ingress.enabled` | Enable ingress | `true` |
| `ingress.className` | Ingress class name | `nginx` |
| `ingress.hosts[0].host` | Hostname | `wordpress.hkhpros.xyz` |
| `ingress.tls` | TLS configuration | `[]` |

### Resource Management

| Parameter | Description | Default |
|-----------|-------------|---------|
| `wordpress.resources.limits.cpu` | CPU limit | `500m` |
| `wordpress.resources.limits.memory` | Memory limit | `512Mi` |
| `wordpress.resources.requests.cpu` | CPU request | `250m` |
| `wordpress.resources.requests.memory` | Memory request | `256Mi` |

### Autoscaling

| Parameter | Description | Default |
|-----------|-------------|---------|
| `autoscaling.enabled` | Enable HPA | `false` |
| `autoscaling.minReplicas` | Minimum replicas | `1` |
| `autoscaling.maxReplicas` | Maximum replicas | `10` |
| `autoscaling.targetCPUUtilizationPercentage` | CPU target | `80` |

## Security Features

- **Non-root containers**: Both WordPress and MySQL run as non-root users
- **Security contexts**: Proper filesystem permissions and user contexts
- **Network policies**: Optional network isolation
- **Secret management**: Passwords stored in Kubernetes secrets
- **Resource limits**: Prevent resource exhaustion

## Monitoring and Observability

- **Health checks**: Liveness and readiness probes
- **Prometheus labels**: Standard labels for monitoring
- **Structured logging**: Application logs accessible via kubectl

## Backup and Recovery

### Database Backup

```bash
kubectl exec -it deployment/my-wordpress-mysql -- mysqldump -u root -p wordpress > backup.sql
```

### WordPress Files Backup

```bash
kubectl cp my-wordpress-pod:/var/www/html ./wordpress-backup
```

## Upgrading

```bash
helm upgrade my-wordpress ./wordpress-helm
```

## Uninstalling

```bash
helm uninstall my-wordpress
```

**Note**: This will not delete PVCs. Delete them manually if needed:

```bash
kubectl delete pvc -l app.kubernetes.io/instance=my-wordpress
```

## Troubleshooting

### Common Issues

1. **Pods not starting**: Check resource limits and node capacity
2. **Database connection issues**: Verify MySQL service and credentials
3. **Ingress not working**: Ensure ingress controller is installed
4. **Storage issues**: Check PV provisioner and storage class

### Debug Commands

```bash
# Check pod status
kubectl get pods -l app.kubernetes.io/instance=my-wordpress

# View logs
kubectl logs -f deployment/my-wordpress

# Describe resources
kubectl describe deployment my-wordpress
kubectl describe pvc my-wordpress-wordpress

# Test database connection
kubectl exec -it deployment/my-wordpress-mysql -- mysql -u wordpress -p
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

This chart is licensed under the Apache 2.0 License.