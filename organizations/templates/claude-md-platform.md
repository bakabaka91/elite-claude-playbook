# Team: [Platform/Infra]

## 1. Team Identity

- **Team name:** [Platform / Infrastructure / DevOps / SRE]
- **Mission:** [One-line: what does this team own and operate?]
- **Slack channel:** #[team-slack-channel]
- **On-call rotation:** [Link to on-call schedule, PagerDuty, etc.]
- **Tech lead:** [Name] (@github-handle)
- **SLO targets:** [Uptime percentage, RTO, RPO]
- **Change Advisory Board (CAB):** [Who approves production changes? When do they meet?]

## 2. Stack

- **Infrastructure as Code:** [Terraform / Pulumi / CloudFormation / Helm]
- **Cloud provider:** [AWS / GCP / Azure / Multi-cloud]
- **Container orchestration:** [Kubernetes / ECS / App Engine / None]
- **Observability:** [DataDog / Prometheus + Grafana / New Relic / Cloudwatch]
- **Logging:** [ELK Stack / Splunk / CloudWatch / Datadog]
- **CI/CD platform:** [GitHub Actions / GitLab CI / CircleCI / Jenkins]
- **DNS:** [Route 53 / CloudFlare / Internal]
- **Certificate authority:** [AWS ACM / Let's Encrypt / Internal CA]

## 3. Environment Topology

- **Environments:**
  - **Development:** [AWS account ID / GCP project / etc.]
    - Region: [us-east-1 / etc.]
    - Kubernetes cluster: [dev-cluster-1]
    - Database: [Postgres RDS - dev instance]
  - **Staging:** [AWS account ID / GCP project / etc.]
    - Region: [us-east-1 / etc.]
    - Kubernetes cluster: [staging-cluster]
    - Database: [Postgres RDS - staging instance] (production snapshot)
  - **Production:** [AWS account ID / GCP project / etc.]
    - Region: [us-east-1 (primary), us-west-1 (backup)]
    - Kubernetes cluster: [prod-cluster-1, prod-cluster-2]
    - Database: [Postgres RDS - multi-AZ, automated backups]

- **DNS domains:**
  - Dev: `*.dev.company.internal`
  - Staging: `*.staging.company.internal`
  - Production: `*.company.internal`

- **Networking:**
  - VPC CIDR: [10.0.0.0/8]
  - Public subnets: [list]
  - Private subnets: [list]
  - NAT gateways: [locations]

## 4. Commands

- `terraform plan -var-file=prod.tfvars` — Preview infrastructure changes
- `terraform apply -var-file=prod.tfvars` — Apply infrastructure changes
- `kubectl apply -f manifests/` — Deploy Kubernetes manifests
- `kubectl get nodes` — Check cluster health
- `kubectl logs -f [pod-name]` — Stream pod logs
- `./.github/workflows/deploy.yml` — Trigger deployment pipeline
- `aws ec2 describe-instances` — List EC2 instances
- `aws rds describe-db-instances` — Check RDS instances
- `make deploy-staging` — Deploy to staging (custom make target)
- `make deploy-production` — Deploy to production (requires approval)

## 5. Change Management

- **Who approves changes:** [Change Advisory Board members]
- **Change request process:** [JIRA ticket / ServiceNow / Linear / GitHub Issue]
- **Review required for:**
  - All production database schema changes
  - All infrastructure changes (compute, storage, networking)
  - All security policy changes
  - Dependency upgrades to critical libraries
  - Certificate rotations
- **Change freeze windows:** [Dates when no changes allowed except hotfixes]
- **Deployment schedule:** [Deployments happen [Monday-Friday / specific days]]
- **Approvals before deploy:**
  - [ ] Code review (peer)
  - [ ] Security review (if relevant)
  - [ ] CAB approval (for production)
  - [ ] Deployment runbook updated (if needed)

## 6. SLO / SLA Definitions

- **Uptime SLO:** [99.9% / 99.95% / 99.99%]
  - Monthly acceptable downtime: [43 minutes / 21 minutes / 4 minutes]
- **RTO** (Recovery Time Objective): [15 minutes / 1 hour / 4 hours]
- **RPO** (Recovery Point Objective): [5 minutes / 1 hour / 24 hours]
- **On-call response time:** [Alert page → acknowledged within 15 minutes]
- **Critical incident resolution:** [P0 within 4 hours / P1 within 24 hours]

## 7. Incident Response & Runbooks

- **Incident escalation:**
  - Automated alert → on-call engineer (PagerDuty)
  - No response in 15 minutes → escalate to tech lead
  - Still unresolved after 1 hour → escalate to VP/Eng
- **Status page:** [Link to public status page]
- **Runbooks location:** [Link to runbook repo / wiki]
- **Common incidents & runbooks:**
  - [Database connection pool exhaustion](runbooks/db-pool-exhaustion.md)
  - [Kubernetes node failure](runbooks/k8s-node-failure.md)
  - [Network latency spike](runbooks/network-latency.md)

## 8. Monitoring & Alerting

- **Metrics dashboard:** [DataDog / Grafana dashboard URL]
- **Critical metrics to monitor:**
  - API error rate (alert if > 1%)
  - API p99 latency (alert if > 1000ms)
  - Database connection pool usage (alert if > 80%)
  - Disk usage (alert if > 85%)
  - Memory usage (alert if > 90%)
  - CPU usage (alert if > 80%)
- **Log aggregation:** [Splunk / ELK / Datadog]
- **Distributed tracing:** [Datadog / Jaeger / None]

## 9. Security & Compliance

- **Network security:**
  - All traffic encrypted (HTTPS / mTLS)
  - Security groups / Network policies restrict inbound/outbound
  - No public access to databases
- **Secrets management:** [AWS Secrets Manager / HashiCorp Vault / Doppler]
- **Access control:**
  - IAM roles follow least-privilege principle
  - MFA required for all AWS / cloud provider access
  - SSH keys rotated [quarterly / annually]
- **Backup & disaster recovery:**
  - Backups retained for [30 days / 90 days / 1 year]
  - Backups tested [monthly / quarterly]
  - RTO/RPO tested annually
- **Compliance obligations:** [SOC 2 / HIPAA / GDPR / PCI-DSS / FedRAMP]
  - Audit logging [enabled / disabled]
  - Compliance reports generated [monthly / quarterly / annually]

## 10. Common Mistakes to Avoid

- NEVER hardcode secrets in Terraform or config files — use vault
- NEVER deploy to production on a Friday afternoon — deploy Monday-Thursday only
- NEVER skip the CAB approval process — all changes must be tracked
- NEVER apply Terraform without running `plan` first
- NEVER delete production databases or backups without explicit approval
- NEVER change production credentials without rotating keys immediately after
- NEVER merge infrastructure code without peer review
- NEVER skip runbook updates after an incident
