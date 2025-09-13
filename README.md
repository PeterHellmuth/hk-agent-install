# KubeMind Agent - Kubernetes Monitoring

A lightweight monitoring agent for Kubernetes clusters that provides comprehensive cluster monitoring and connects to your monitoring dashboard.

## Quick Install

```bash
kubectl apply -f https://raw.githubusercontent.com/peterhellmuth/kubemind-agent-install/main/install/kubemind-agent.yaml
```

## Verify Installation

```bash
# Check if the agent is running
kubectl get pods -n kubemind-agent

# View agent logs
kubectl logs -n kubemind-agent -l app=kubemind-agent -f

# Check service status
kubectl get svc -n kubemind-agent
```

You should see output like:
```
NAME       READY   STATUS    RESTARTS   AGE
kubemind-agent   1/1     Running   0          30s
```

And logs showing:
```
[2024-08-21 12:34:56] KubeMind Agent Service starting...
[2024-08-21 12:34:56] Agent is running at: 08/21/2024 12:34:56 +00:00
```

## What It Does

The KubeMind Agent automatically:
- Monitors cluster health and resources
- Tracks pod deployments and status
- Reports cluster metrics to your dashboard
- Provides real-time insights into your K8s environment

## Requirements

- Kubernetes cluster (K3s, EKS, GKE, AKS, etc.)
- `kubectl` configured with cluster access
- Cluster admin permissions for initial install

## Configuration

The agent works out-of-the-box with default settings. For custom configuration, download and edit the install file:

```bash
# Download the install file
curl -O https://raw.githubusercontent.com/peterhellmuth/kubemind-agent-install/main/install/kubemind-agent.yaml

# Edit configuration (optional)
# - Change resource limits
# - Add custom environment variables
# - Modify namespace

# Apply your customized version
kubectl apply -f kubemind-agent.yaml
```

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `DOTNET_ENVIRONMENT` | `Production` | Runtime environment |
| `LOG_LEVEL` | `Information` | Logging level |

## Updating

The agent automatically pulls the latest version when pods restart. To force an update:

```bash
kubectl rollout restart deployment/kubemind-agent -n kubemind-agent
```

## Uninstall

```bash
kubectl delete -f https://raw.githubusercontent.com/peterhellmuth/kubemind-agent-install/main/install/kubemind-agent.yaml
```

This removes all KubeMind Agent components from your cluster.

## Troubleshooting

### Agent Pod Not Starting

```bash
# Check pod details
kubectl describe pod -n kubemind-agent -l app=kubemind-agent

# Check events
kubectl get events -n kubemind-agent --sort-by='.lastTimestamp'
```

### Image Pull Issues

If you see `ImagePullBackOff` errors, ensure your cluster can access Docker Hub:

```bash
# Test connectivity
kubectl run test-pull --image=peterhellmuth/kubemind-agent:latest --rm -it --restart=Never -- echo "Image pull test"
```

### Permission Issues

The agent requires certain permissions to monitor your cluster. If you see permission errors:

```bash
# Check if the service account has proper permissions
kubectl auth can-i get pods --as=system:serviceaccount:kubemind-agent:default
```

### Resource Constraints

If the agent is being killed due to resource limits:

```bash
# Check resource usage
kubectl top pod -n kubemind-agent

# View resource limits
kubectl describe deployment kubemind-agent -n kubemind-agent
```

You can increase resource limits by editing the install file before applying.

## Support

- **Issues**: [Report problems here](https://github.com/peterhellmuth/kubemind-agent-install/issues)
- **Documentation**: See the [docs/](docs/) directory
- **Updates**: Watch this repository for notifications of new releases

## Security

The KubeMind Agent:
- Runs with minimal required permissions
- Uses read-only access to cluster resources
- Connects outbound only (no inbound ports exposed)
- Runs as non-root user inside the container

## Compatibility

Tested and supported on:
- K3s
- Amazon EKS
- Google GKE
- Azure AKS
- Self-managed Kubernetes 1.20+

## Versions

- **latest** - Latest stable version (recommended for most users)
- **stable** - Long-term stable release
- **v1.x.x** - Specific version tags (coming soon)

Current version uses image: `peterhellmuth/kubemind-agent:latest`

---

**Note**: This agent is designed for monitoring purposes and requires outbound internet connectivity to report metrics to your monitoring dashboard.