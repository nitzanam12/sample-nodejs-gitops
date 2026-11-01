# sample-nodejs-gitops

Helm chart and Argo CD configuration for sample-nodejs on Amazon EKS.

Application repository: [https://github.com/nitzanam12/sample-nodejs](https://github.com/nitzanam12/sample-nodejs)

## Files

```text
.github/workflows/ci.yml
argocd/sample-nodejs.yaml
helm/sample-nodejs/
  Chart.yaml
  values.yaml
  templates/
    deployment.yaml
    service.yaml
    ingress.yaml
    hpa.yaml
```

The Helm chart deploys the application with readiness and liveness probes, resource limits, a ClusterIP Service, and an ALB Ingress. An HPA scales the application between 2 and 4 replicas based on CPU utilization. Rolling updates use maxSurge: 1 and maxUnavailable: 0.

HTTPS uses an ACM certificate, with HTTP redirected to HTTPS. Only /my-app and /about are publicly accessible.

## GitOps CI

Pull requests to main run Helm lint and template validation before merging.

## Deployment flow

1. GitHub Actions in the application repository builds and scans the image, then pushes it to ECR with a commit SHA tag.
2. A GitHub App opens a PR updating image.tag in values.yaml.
3. GitOps CI validates the Helm chart before the PR is reviewed and merged.
4. Argo CD automatically syncs the Helm chart to EKS after the merge.

A separate GitOps repository keeps the desired deployment state independent of application code. It allows deployment permissions to be managed separately, and lets Argo CD deploy changes without giving CI direct access to the cluster.
