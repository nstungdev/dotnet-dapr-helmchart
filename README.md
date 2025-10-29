# .NET 8 Application with Dapr Helm Chart

This repository contains a Helm chart for deploying .NET 8 applications with Dapr sidecar on Kubernetes.

## Project Structure

```
dotnet-dapr-helmchart/
├── README.md
└── dotnet-dapr-app/          # Helm chart
    ├── Chart.yaml            # Chart metadata
    ├── values.yaml           # Default configuration values
    ├── README.md             # Chart usage guide
    └── templates/            # Kubernetes templates
        ├── deployment.yaml   # Deployment with Dapr annotations
        ├── service.yaml      # Service
        ├── configmap.yaml    # ConfigMap
        ├── ingress.yaml      # Ingress
        ├── serviceaccount.yaml # ServiceAccount
        ├── NOTES.txt         # Post-install instructions
        └── _helpers.tpl      # Helper templates
```

## Features

✅ **Dapr Integration**: Automatically injects Dapr sidecar with proper annotations  
✅ **.NET 8 Support**: Optimized for .NET 8 applications  
✅ **Health Checks**: Pre-configured health check endpoints  
✅ **ConfigMap**: Support for mounting configuration files  
✅ **Ingress**: Easy external exposure  
✅ **Scalable**: Horizontal scaling support  
✅ **Production Ready**: Includes security contexts, resource limits  

## Quick Start

1. **Install Dapr on cluster**:
   ```bash
   dapr init -k
   ```

2. **Deploy application**:
   ```bash
   # Clone repository
   git clone https://github.com/nstungdev/dotnet-dapr-helmchart.git
   cd dotnet-dapr-helmchart
   
   # Install chart
   helm install my-app ./dotnet-dapr-app \
     --set image.repository=your-registry/your-app \
     --set image.tag=latest
   ```

3. **Check deployment**:
   ```bash
   kubectl get pods
   # Each pod should have 2 containers: app + dapr sidecar
   ```

## Customization

Create a `my-values.yaml` file:

```yaml
image:
  repository: myregistry/my-dotnet-app
  tag: "1.0.0"

replicaCount: 3

dapr:
  appId: my-production-app
  logLevel: info

ingress:
  enabled: true
  hosts:
    - host: myapp.production.com
      paths:
        - path: /
          pathType: Prefix

resources:
  limits:
    cpu: 1000m
    memory: 1Gi
  requests:
    cpu: 500m
    memory: 512Mi
```

Then deploy:
```bash
helm install my-app ./dotnet-dapr-app -f my-values.yaml
```

## Documentation

See [dotnet-dapr-app/README.md](./dotnet-dapr-app/README.md) for detailed configuration and usage information.

## Contributing

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request