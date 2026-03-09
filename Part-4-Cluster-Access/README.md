# Part 4 – Cluster Access & IAM Integration

---

## Objective

Configure secure access to the EKS cluster using IAM-based authentication and Kubernetes RBAC, while enforcing private network access through a bastion host.

This milestone focused on:

- Secure cluster access design
- IAM role integration with EKS
- kubectl authentication via AWS CLI
- Debugging IAM and RBAC authorization errors

---

## Access Design Decisions

### Bastion Host in Private Subnet

The bastion host is deployed in a private subnet and accessed using:

- AWS Systems Manager (SSM)
- IAM-based session management

This removes the need for:

- Public IP exposure
- SSH key management
- Open inbound security group rules

This design follows production security best practices.

---

## IAM-Based Authentication

Cluster authentication is performed using AWS IAM.

```bash
aws eks update-kubeconfig --region us-east-1 --name my-eks-cluster
```

This command:

- Updates the kubeconfig file
- Configures kubectl to use AWS IAM authentication
- Enables secure communication with the EKS API server

---

## Access Entry Configuration (EKS Access API)

An IAM access entry was created for the bastion role:

```
arn:aws:iam::<account-id>:role/mtier-ssm-role
```

The following policy was attached:

- AmazonEKSClusterAdminPolicy

This allowed the bastion IAM role to perform administrative actions at the Kubernetes cluster level.

---

## Problem Encountered

After configuring kubeconfig, running:

```bash
kubectl get nodes
```

Returned:

```
Error from server (Forbidden): nodes is forbidden
```

The role was authenticated but not authorized to perform cluster-level actions.

---

## Root Cause

The IAM role attached to the bastion host was not mapped to a Kubernetes RBAC role with sufficient permissions.

Authentication (IAM) succeeded, but authorization (RBAC) failed.

---

## Resolution

Created an IAM Access Entry in the EKS console and attached:

- AmazonEKSClusterAdminPolicy

After updating permissions:

```bash
kubectl get nodes
```

Successfully returned cluster nodes.

---

## Key Concepts Demonstrated

- IAM vs Kubernetes RBAC separation
- EKS Access Entry API
- Role-based authentication debugging
- Secure bastion architecture using SSM
- AWS STS identity verification
- Production-style cluster access design

---

## Outcome

Successfully implemented secure, IAM-based access control for a private EKS cluster using:

- Bastion host in private subnet
- AWS Systems Manager
- IAM Access Entries
- Kubernetes RBAC

This milestone demonstrates real-world EKS security configuration and troubleshooting experience.
