# sample-nodejs-gitops

Helm chart and Argo CD configuration for sample-nodejs on Amazon EKS.

Application repository: https://github.com/nitzanam12/sample-nodejs

## Files

    argocd/sample-nodejs.yaml
    helm/sample-nodejs/
      Chart.yaml
      values.yaml
      templates/
        deployment.yaml
        service.yaml
        ingress.yaml

The Helm chart deploys the application with readiness and liveness probes, resource limits, a ClusterIP Service, and an ALB Ingress. An HPA scales the application between 2 and 4 replicas based on CPU utilization. Rolling updates use maxSurge: 1 and maxUnavailable: 0.

HTTPS uses an ACM certificate, with HTTP redirected to HTTPS. Only /my-app and /about are publicly accessible.

## Deployment flow

1. GitHub Actions builds and scans the image, then pushes it to ECR with a commit SHA tag.
2. A GitHub App opens a PR updating image.tag in values.yaml.
3. After the PR is merged, Argo CD automatically syncs the Helm chart to EKS.

The GitOps PR is reviewed before deployment.