# 🚀 Production-Ready EKS Cluster using Custom Terraform Modules

## 📌 Project Overview

This project provisions a **production-style Amazon EKS (Elastic Kubernetes Service) cluster** using **custom-built Terraform modules** instead of community modules.

The goal of this project is to demonstrate:

* ✅ Deep understanding of AWS networking (VPC, subnets, NAT, route tables)
* ✅ Secure Kubernetes cluster architecture (private worker nodes)
* ✅ Modular Infrastructure as Code (custom Terraform modules)
* ✅ IAM best practices (least privilege + IRSA)
* ✅ KMS encryption for Kubernetes secrets
* ✅ Optional secrets management using AWS Secrets Manager

This is not a “copy-paste” EKS module setup — every resource is explicitly defined and structured for learning and production clarity.

---

## 🏗️ Architecture Overview

The infrastructure follows a secure, production-style layered design:

### 🌐 Networking (VPC Module)

* Custom VPC (`10.0.0.0/16`)
* 3 Public Subnets (Multi-AZ)
* 3 Private Subnets (Multi-AZ)
* Internet Gateway (for public access)
* Single NAT Gateway (cost optimized)
* Route Tables & Associations
* Required Kubernetes subnet tagging

### 🔐 Security & IAM (IAM Module)

* Dedicated IAM Role for EKS Control Plane
* Dedicated IAM Role for Worker Nodes
* AWS Managed Policy Attachments
* OIDC Provider for IRSA (IAM Roles for Service Accounts)

### ☸️ Kubernetes (EKS Module)

* EKS Cluster (Kubernetes v1.31)
* KMS encryption for Kubernetes secrets
* CloudWatch log group
* Custom Security Groups (Cluster + Node)
* Managed Node Groups:

  * General (On-Demand)
  * Spot (Cost-Optimized)
* Launch Templates with:

  * Encrypted EBS
  * IMDSv2 enforced
  * Monitoring enabled
* EKS Add-ons:

  * CoreDNS
  * kube-proxy
  * VPC CNI

### 🔑 Optional Secrets Manager Module

* Conditional secret creation
* KMS encryption
* IAM policy for reading secrets
* Supports:

  * Database credentials
  * API keys
  * App configuration

---

## 🧠 Why Custom Modules?

| Public Module              | This Project                      |
| -------------------------- | --------------------------------- |
| Abstracted internals       | Full visibility of every resource |
| Limited customization      | Fully customizable                |
| Learning is hidden         | Learn how EKS actually works      |
| Community updates required | Self-controlled module design     |

This project demonstrates real infrastructure understanding — not just module usage.

---

## 🔄 Traffic Flow

1. User → Public Load Balancer
2. Load Balancer → Private Worker Nodes
3. Worker Nodes → Pods
4. Pods → NAT Gateway → Internet (outbound only)

🔒 Worker nodes remain private and are never directly exposed to the internet.

---

## 📂 Project Structure

```
.
├── main.tf
├── provider.tf
├── variables.tf
├── outputs.tf
├── modules/
│   ├── vpc/
│   ├── iam/
│   ├── eks/
│   │   └── templates/userdata.sh
│   └── secrets-manager/
```

---

## 🚀 Deployment Steps

### 1️⃣ Initialize

```bash
terraform init
```

### 2️⃣ Validate

```bash
terraform validate
```

### 3️⃣ Review Plan

```bash
terraform plan
```

### 4️⃣ Apply

```bash
terraform apply
```

Provisioning time: ~20–25 minutes.

---

## 🔗 Configure kubectl

After deployment:

```bash
aws eks --region us-east-1 update-kubeconfig --name day20-eks
```

Verify:

```bash
kubectl get nodes
kubectl get pods -A
```

---

## 🧪 Test Application Deployment

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=LoadBalancer
kubectl get svc
```

This creates a public AWS Load Balancer routed to private worker nodes.

---

## 🔐 Security Highlights

* Private worker nodes (no public IP)
* Outbound internet via NAT only
* KMS encryption for Kubernetes secrets
* IMDSv2 enforced
* Least privilege IAM roles
* IRSA enabled (pod-level IAM access)
* Security groups with controlled ingress rules
* Multi-AZ deployment for high availability

---

## 💰 Cost Awareness

Estimated monthly cost (us-east-1):

* EKS Control Plane: ~$73
* EC2 Instances: ~$70–90
* NAT Gateway: ~$32
* EBS + Data Transfer: Variable

Estimated total: ~$180–220/month

This setup is intended for learning or short-term deployment.
Always destroy resources when not in use.

---

## 🧹 Cleanup

Before destroying:

```bash
kubectl delete svc --all
```

Then:

```bash
terraform destroy
```

---

## 🎓 Key Learning Outcomes

By building this project from scratch, you gain:

* Deep AWS networking understanding
* Production-style EKS architecture knowledge
* Terraform module design skills
* Kubernetes service exposure patterns
* Security-first infrastructure thinking
* Conditional resource creation patterns
* Real-world DevOps architecture experience

---

## 🏁 Next Improvements

* Add RDS module
* Add AWS Load Balancer Controller
* Add Ingress Controller
* Add Cluster Autoscaler
* Add CI/CD pipeline (GitHub Actions)
* Implement GitOps (ArgoCD)

---

## 📜 License

For educational and demonstration purposes.

---

**Author:** Anjali Yadav
**Project Type:** Infrastructure as Code / DevOps
**Tools:** Terraform, AWS, Kubernetes (EKS)
