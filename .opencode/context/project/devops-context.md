# DevOps Engineer Context

## Key Commands
- `terraform init`: Initialize Terraform working directory
- `terraform plan`: Preview infrastructure changes before applying
- `terraform apply`: Apply infrastructure changes (require approval first)
- `terraform validate`: Validate Terraform configuration syntax
- `kubectl apply -f manifest.yaml`: Apply Kubernetes manifest
- `kubectl dry-run=client`: Preview K8s changes without applying
- `docker build -t image:tag .`: Build Docker container image
- `docker scan image:tag`: Scan Docker image for vulnerabilities

## File Structure
- Terraform modules: `infrastructure/modules/{module-name}/`
- Terraform environments: `infrastructure/environments/{env}/`
- Terraform state: Remote backend (S3, GCS, Azure Storage) with locking
- K8s manifests: `k8s/{namespace}/{resource-type}/`
- Dockerfiles: `docker/{service}/Dockerfile`
- CI/CD pipelines: `.github/workflows/` or `.gitlab-ci.yml` or `Jenkinsfile`

## Code Style

### Terraform Conventions
- Use modules for reusable components (VPC, RDS, EKS, etc.)
- Store state remotely with locking enabled
- Version pin all providers: `required_version = "~> 1.5.0"`
- Use workspaces for environments (dev, staging, prod)
- Never hardcode credentials or secrets
- Use `terraform fmt` for consistent formatting
- Use descriptive variable names: `rds_instance_class`, not `instance_type`

### Kubernetes Conventions
- No privileged containers unless explicitly required and justified
- Read-only root filesystems where possible
- Drop all capabilities, add only required ones
- Define resource limits and requests for all containers
- Include liveness and readiness probes
- Use namespaces to isolate workloads
- Apply pod security standards (restricted, baseline, or privileged)

### Docker Conventions
- Multi-stage builds for smaller final images
- Run containers as non-root user
- Use distroless or minimal base images (alpine, scratch)
- No secrets in image layers (use secrets management instead)
- Scan images for vulnerabilities before deployment
- Use .dockerignore to exclude unnecessary files

## Workflow Rules

### CI/CD Pipeline Stages
1. **Build** → Compile application and create artifacts
2. **Test** → Unit tests, integration tests, security scans
3. **Security Scan** → SAST (static analysis), DAST (dynamic analysis), dependency scanning
4. **Package** → Create container images or deployment packages
5. **Deploy** → Deploy to environment with approval gates
6. **Verify** → Smoke tests, health checks, integration validation
7. **Monitor** → Observability, alerting, logging

### Secret Management Best Practices
**NEVER hardcode secrets in:**
- Terraform files
- Kubernetes manifests
- Dockerfiles
- CI/CD pipeline configurations
- Environment variable files committed to git

**USE proper secret management:**
- HashiCorp Vault for centralized secret storage
- AWS Secrets Manager / Azure Key Vault / GCP Secret Manager for cloud-native secrets
- Kubernetes Secrets with encryption at rest enabled
- CI/CD secret stores (GitHub Secrets, GitLab CI Variables)
- External Secrets Operator for K8s integration with external secret stores

### IAM Least Privilege Rules
- Grant minimum required permissions for the task
- Use roles instead of users where possible
- Regular access reviews (quarterly recommended)
- No wildcard permissions (*) unless absolutely necessary
- Separate roles for different environments (dev, staging, prod)
- Enable MFA for privileged operations
- Use temporary credentials where possible (STS, OIDC)

## Common Patterns

### Terraform Module Pattern
```hcl
# modules/s3-bucket/main.tf
resource "aws_s3_bucket" "bucket" {
  bucket = var.bucket_name
  
  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }
  
  versioning {
    enabled = true
  }
  
  acl = "private"
}
```

### Kubernetes Security Context Pattern
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - name: app
    image: myapp:1.0
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
          - ALL
    resources:
      limits:
        memory: "256Mi"
        cpu: "500m"
      requests:
        memory: "128Mi"
        cpu: "250m"
```

### Docker Multi-Stage Build Pattern
```dockerfile
# Build stage
FROM golang:1.20 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o app

# Final stage
FROM gcr.io/distroless/static-debian11
COPY --from=builder /app/app /
USER nonroot:nonroot
ENTRYPOINT ["/app"]
```

## Before Committing
1. Run `terraform validate` to check syntax
2. Run `terraform plan` and review all changes
3. Ensure no hardcoded secrets or credentials
4. Verify remote state is configured correctly
5. Check that all IAM policies follow least privilege
6. Scan Docker images for vulnerabilities
7. Validate Kubernetes manifests with `kubectl dry-run`
8. Ensure CI/CD pipeline includes security scanning stages
