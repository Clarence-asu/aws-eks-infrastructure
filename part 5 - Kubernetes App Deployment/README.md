# Kubernetes Milestone 5

This folder contains Kubernetes manifests used to deploy an application in an EKS cluster.

Resources created:

- Namespace
- ConfigMap
- Secret
- Deployment
- LoadBalancer Service

---

## Milestone 5 - Kubernetes App Deployment Summary

### What Was Done
Deployed a full Kubernetes application onto the EKS cluster by applying 5 manifest files in order:

1. **Namespace** — created an isolated environment called `demo`
2. **Secret** — securely stored app credentials
3. **ConfigMap** — stored app configuration settings
4. **Deployment** — launched the app across 2 running pods
5. **Load Balancer** — exposed the app publicly to the internet

---

### Obstacles Encountered

 `kubectl apply -f .` Failing

**Problem:** Running the command in the project folder failed because `README.md` was in the same folder and kubectl can't read it — only `.yaml/.yml/.json` files are accepted.

**Solution:** Navigated into the specific Part 5 folder and applied each YAML file individually in the correct order.
