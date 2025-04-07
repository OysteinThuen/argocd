# bootstrap

Contains Helm charts and related manifests for configuring the two Argo CD clusters themselves, using Argo CD.

- `prod/` - Argo CD production cluster.
- `dev/` - Argo CD dev cluster.

## Best practices

### Adding a new Helm installation

1. Create a file called `<chart-name>.yml` in the `dev/` or `prod/` directory.
   We will assume you are using the dev cluster for this example.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: <chart-name>
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/OysteinThuen/argocd.git
    targetRevision: HEAD
    path: 'manifests/bootstrap/dev/argocd'
  destination:
    server: https://kubernetes.default.svc
    namespace: <chart-namespace>
  syncPolicy:
    automated:
      selfHeal: true
```

2. Create a folder called `<chart-name>` in the `dev/` directory.

```bash
mkdir dev/<chart-name>
```

3. Add an umbrella chart in the file `dev/<chart-name>/Chart.yaml` to install the Helm chart.

```yaml
apiVersion: v2
name: <chart-name>-umbrella
version: 0.1.0
dependencies:
  - name: <chart-name>
    version: <chart-version>
    repository: <chart-repository>
```

4. **Optional**: Add a `values.yaml` file in the `dev/<chart-name>` directory to override the default values of the Helm chart.

```yaml
<chart-name>:
  image:
    tag: v1.2.3
```

5. **Optional**: Add other manifests in the `dev/<chart-name>/templates` directory to create other Kubernetes resources in the same namespace as the Helm chart.
