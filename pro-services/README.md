# pro-services Helm chart

This chart deploys a modern Kubernetes workload with sensible defaults.

- Features included:
- Deployment with liveness/readiness probes
- Service (ClusterIP by default)
- Ingress (optional)
- HorizontalPodAutoscaler (optional)
- ServiceAccount
- ConfigMap & Secret templates
- PodDisruptionBudget
- NetworkPolicy (optional)

Ingress and AWS ALB
---------------------

This chart provides a simple pre-filled set of annotations for use with the AWS ALB Ingress Controller / AWS Load Balancer Controller. The defaults assume an internet-facing ALB that targets pod IPs. These defaults live in `values.yaml` under `ingress.annotations` and include:

- `kubernetes.io/ingress.class: "alb"`
- `alb.ingress.kubernetes.io/scheme: "internet-facing"`
- `alb.ingress.kubernetes.io/target-type: "ip"`
- `alb.ingress.kubernetes.io/healthcheck-path: "/"`
- `alb.ingress.kubernetes.io/listen-ports: '[{"HTTP":80}]'`
- `alb.ingress.kubernetes.io/certificate-arn: ""` (empty by default; set for HTTPS)

To enable ingress with the ALB defaults:

    helm install my-release ./pro-services --set ingress.enabled=true

If you want TLS, set the certificate ARN in values or via `--set ingress.annotations."alb.ingress.kubernetes.io/certificate-arn"=arn:aws:acm:...` and update `listen-ports` accordingly.

Versioning & publishing
-----------------------

You can tag and publish chart packages so they can be referenced by version. This repository includes a GitHub Actions workflow that packages the chart and creates a GitHub Release when you push a git tag matching `v*.*.*` (for example `v0.1.0`).

Locally you can create a packaged chart with:

```bash
./scripts/package-chart.sh
```

This writes a `*.tgz` chart archive into `dist/` which you can upload to an OCI registry or attach to a GitHub Release. The CI workflow will automatically create a Release and upload the packaged chart when you push a matching tag.

To publish to an OCI registry (optional):

1. Login to an OCI registry (example GitHub Container Registry):

```bash
helm registry login ghcr.io -u <username> -p <token>
```

2. Push the chart (example):

```bash
helm push dist/pro-services-0.1.0.tgz oci://ghcr.io/<owner>/<repo>
```


Usage examples:

Render templates locally:

    helm template my-release ./pro-services

Install to cluster:

    helm install my-release ./pro-services
