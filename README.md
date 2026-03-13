# AWS EKS Infrastructure

I built this project to get hands-on experience deploying a real Kubernetes environment on AWS from the ground up. Rather than just reading about how EKS works, I wanted to actually go through the process of setting up the networking, creating the cluster, deploying an app, and managing it — all the way through cleanup.

---

## Why I Built This

I wanted to understand how all the pieces of a cloud infrastructure fit together. A lot of tutorials skip over the details or hand you a pre-built environment. This project forced me to build everything myself — the VPC, the cluster, the node group, the app deployment — and troubleshoot the real errors that came up along the way.

---

## What I Used

- **AWS EKS** — to run the Kubernetes cluster
- **AWS CloudFormation** — to build the infrastructure using code instead of clicking through the console
- **AWS Systems Manager (SSM)** — to access the EC2 instances securely without needing SSH
- **kubectl** — to manage everything inside the cluster
- **IAM** — to set up the right roles and permissions
- **Elastic Load Balancer** — to expose the app to the internet

---

## How the Project Was Structured

| Part | What I Did |
|------|------------|
| Part 1 - Network Stack | Set up the VPC and subnets that everything else runs on |
| Part 2 - EKS Cluster Stack | Created the EKS cluster using a CloudFormation template |
| Part 3 - Node Group | Added worker nodes so the cluster could actually run workloads |
| Part 4 - Cluster Access | Configured IAM roles and kubeconfig to connect to the cluster |
| Part 5 - Kubernetes App Deployment | Deployed a live app using Kubernetes manifest files |
| Part 6 - Rolling Updates & Scaling | Scaled the app, restarted the deployment, updated configs, and tested rollbacks |
| Part 7 - Cleanup | Deleted everything in the right order to avoid leaving orphaned resources |

---

## What I Ended Up With

By the end of the project I had a working Kubernetes app running on AWS with a public URL served through a load balancer. The whole infrastructure was built using CloudFormation templates, and I managed the app entirely through kubectl.

---

## What I Learned

Going through this project taught me how networking, IAM, and Kubernetes all connect in a real AWS environment. I also ran into plenty of real errors — permission issues, nodes hitting capacity, pods getting stuck — and had to figure out how to fix them. That troubleshooting experience was honestly just as valuable as getting everything working.
