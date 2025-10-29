# .NET 8 Application with Dapr Helm Chart

This Helm chart is designed to deploy .NET 8 applications with Dapr sidecar on Kubernetes.

## Requirements

- Kubernetes 1.16+
- Helm 3.0+
- Dapr installed on the cluster

## Installation

### 1. Install Dapr on cluster (if not already installed)

```bash
# Install Dapr CLI
curl -fsSL https://raw.githubusercontent.com/dapr/cli/master/install/install.sh | /bin/bash

# Initialize Dapr on Kubernetes
dapr init -k
```

### 2. Prepare Docker image

Make sure you have built and pushed your .NET 8 application Docker image to a registry.

### 3. Install the chart

```bash
# Install with default configuration
helm install my-dotnet-app ./dotnet-dapr-app

# Or install with custom values
helm install my-dotnet-app ./dotnet-dapr-app -f custom-values.yaml
```

## Configuration

### Main Values

| Parameter | Description | Default Value |
|-----------|-------------|---------------|
| `image.repository` | Docker image repository | `your-registry/dotnet-dapr-app` |
| `image.tag` | Docker image tag | `""` (uses appVersion) |
| `replicaCount` | Number of replicas | `1` |
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `8080` |
| `dapr.enabled` | Enable/disable Dapr | `true` |
| `dapr.appId` | Dapr app ID | `dotnet-dapr-app` |
| `dapr.appProtocol` | App protocol | `http` |
| `ingress.enabled` | Enable/disable Ingress | `false` |

### Custom Values Example

```yaml
# custom-values.yaml
image:
  repository: myregistry/my-dotnet-app
  tag: "1.0.0"

replicaCount: 2

service:
  type: LoadBalancer
  port: 80

dapr:
  enabled: true
  appId: my-dotnet-app
  appProtocol: http
  logLevel: debug

ingress:
  enabled: true
  className: nginx
  hosts:
    - host: my-app.example.com
      paths:
        - path: /
          pathType: Prefix

app:
  environment: Production
  connectionStrings:
    defaultConnection: "Server=db;Database=MyApp;..."
  healthCheck:
    enabled: true

resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 256Mi
```

## Dapr Features

This chart supports the following Dapr features:

1. **Service Invocation**: Call services through Dapr
2. **State Management**: State management
3. **Pub/Sub**: Messaging
4. **Bindings**: Connect with external systems
5. **Secrets**: Secret management
6. **Configuration**: Configuration management

### Using Dapr in .NET app

```csharp
// Program.cs
using Dapr.Client;
using Dapr.Extensions.Configuration;

var builder = WebApplication.CreateBuilder(args);

// Add Dapr client
builder.Services.AddDapr();

// Add Dapr configuration
builder.Configuration.AddDapr();

var app = builder.Build();

// Use cloud events for pub/sub
app.UseCloudEvents();

// Map Dapr endpoints
app.MapSubscribeHandler();

app.Run();
```

## Monitoring and Debugging

### Check Dapr status

```bash
# View pods (each pod should have 2 containers)
kubectl get pods -l app.kubernetes.io/name=dotnet-dapr-app

# View app logs
kubectl logs -l app.kubernetes.io/name=dotnet-dapr-app -c dotnet-dapr-app

# View Dapr sidecar logs
kubectl logs -l app.kubernetes.io/name=dotnet-dapr-app -c daprd
```

### Access Dapr dashboard (if available)

```bash
# Port forward to Dapr dashboard
kubectl port-forward svc/dapr-dashboard 8080:8080 -n dapr-system

# Access: http://localhost:8080
```

## Uninstall

```bash
helm uninstall my-dotnet-app
```

## Troubleshooting

### 1. Pod not starting

```bash
# Check events
kubectl describe pod <pod-name>

# Check logs
kubectl logs <pod-name> -c dotnet-dapr-app
kubectl logs <pod-name> -c daprd
```

### 2. Dapr sidecar not working

- Make sure Dapr is installed: `dapr status -k`
- Check pod annotations
- View daprd container logs

### 3. Health check failures

- Make sure app exposes health check endpoints
- Check port configuration
- View logs for debugging

## License

[MIT License](LICENSE)