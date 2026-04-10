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

Usage examples:

Render templates locally:

    helm template my-release ./pro-services

Install to cluster:

    helm install my-release ./pro-services
