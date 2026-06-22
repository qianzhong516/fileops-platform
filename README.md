# FileOps

## Introduction

FileOps is a sample file-processing platform built to demonstrate modern platform engineering practices on AWS and Kubernetes. Rather than focusing on application features, the project emphasises infrastructure automation, scalability, and operational reliability.

Key areas covered include:

- CI/CD pipelines for application delivery, infrastructure provisioning and GitOps deployments
- High availability across multiple Availability Zones
- Fault tolerance through persistent job storage and worker recovery
- Observability and autoscaling
- Secure secret management and least-privilege access

To generate realistic workloads, file processing is simulated using randomised job durations. In a real-world scenario, these jobs could represent operations such as file compression, image resizing or media transcoding. Multiple workers processing files in parallel to mimic production workloads.

## Repository Structure

| Repository        | Purpose                                       |
| ----------------- | --------------------------------------------- |
| fileops-app       | Application source code                       |
| fileops-infra     | Terraform infrastructure                      |
| fileops-manifests | Kubernetes manifests and GitOps configuration |
| fileops-setup     | One-time bootstrap resources                  |

## Technologies

### Application

- Frontend: NextJS
- Backend: ExpressJS + Drizzle ORM
- Database: PostgreSQL

### Platform

- Amazon EKS
- Terraform
- GitHub Actions
- ArgoCD

### Observability

- Prometheus
- Grafana

## Architecture

### Infrastructure Overview

The platform follows a private-by-default architecture. Customer traffic and administrative traffic are isolated through separate load balancers, while workloads and databases remain inaccessible from the public Internet.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/architecture.png" width="full" alt="">

### Application Flow

The application follows a three-tier architecture, allowing frontend, backend, and database layers to scale independently. Static assets are stored in S3 while database access is restricted to backend services.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/application-flow.png" width="full" alt="">

### Administrative Access

Administrative services are separated from customer traffic through IP-restricted entry points to reduce attack surface.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/administrative-access.png" width="full" alt="">

## Platform Capabilities

### CI/CD Pipelines

Separate pipelines are built for application delivery, infrastructure provisioning and GitOps deployments. By treating Git as the single source of truth, the platform minimises manual intervention during software delivery process to ensure consistent and repeatable deployments.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/cicd.png" width="full" alt="">

### Secret management

SOPS-encrypted secrets are used for bootstrapping the platform components that require initial credentials. E.g, Grafana admin credentials.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/secret-management-1.png" width="full" alt="">

Application secrets are stored in AWS Secrets Manager and retrieved dynamically at runtime. This approach simplifies secret rotation and avoids embedding sensitive values directly in Kubernetes manifests.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/secret-management-2.png" width="full" alt="">

### AutoScaling

Workload and infrastructure are scaled independently to ensure resources grow according to demand. HPA provides resource-based scaling for frontend services, while KEDA provides event-driven scaling based on custom Prometheus metrics for backend services. Karpenter scales infrastructure by adding nodes when there are unscheduled pods.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/autoscaling.png" width="full" alt="">

### Observability

Application and cluster metrics are centralised in Prometheus, visualised through Grafana dashboards, providing engineer visibility into system health and performance. This observability platform enables proactive monitoring, troubleshooting and alerting as the platform evolves.

<img src="https://d2h0bw0fewoyk7.cloudfront.net/diagrams/o11y.png" width="full" alt="">

## Demos

### Fault Tolerance

This video demonstrates that workloads can survive pod restarts without losing progress, because jobs are persisted in a database and locks are time-based.

▶ [Watch Video](https://d2h0bw0fewoyk7.cloudfront.net/videos/fault-tolerance.mp4)

### Autoscaling

This video demonstrates event-driven autoscaling using Prometheus, KEDA and Karpenter.

▶ [Watch Video](https://d2h0bw0fewoyk7.cloudfront.net/videos/autoscaling.mp4)

### CI/CD + GitOps

This video demonstrates separate pipelines for app delivery, infra provisioning and GitOps deployments.

▶ [Watch Video](https://d2h0bw0fewoyk7.cloudfront.net/videos/cicd.mp4)

### Secret Management

This video demonstrates secret management using SOPS.

▶ [Watch Video](https://d2h0bw0fewoyk7.cloudfront.net/videos/secret-management.mp4)

### Observability

This video demonstrates visualising Prometheus metrics through Grafana dashboards.

▶ [Watch Video](https://d2h0bw0fewoyk7.cloudfront.net/videos/o11y.mp4)

## Interesting Highlights

- Use Docker cache mounts with GitHub Actions Cache to save dependency and app compilation caches. Reduced average image build time from ~4 minutes to ~2 minutes.
- Detect leaked secrets before code reaches the repository via Pre-commit hooks.
- Cluster access is managed through EKS access entries. Workload permissions are granted using Pod Identity Associations where supported, with IRSA used for components that do not yet support Pod Identity.

## Future Improvements

- [ ] Reduce job recovery latency through more advanced worker coordination mechanisms.
- [ ] Add container image vulnerability scanning to the application CI pipeline.
- [ ] Extend the observability stack with alert delivery and notification channels.
- [ ] Introduce AI-assisted pull request reviews.
- [ ] Improve Terraform configuration sharing by publishing non-sensitive outputs through dedicated storage.
