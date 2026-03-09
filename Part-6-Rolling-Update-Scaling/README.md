# Kubernetes Milestone 6 — Rolling Updates & Scaling

This section covers rolling updates, scaling, and rollback operations performed on the EKS cluster deployment.

Resources updated:

- Deployment (scaled and restarted)
- ConfigMap (updated)
- Rollout (undone/rolled back)

---

## Milestone 6 - Rolling Updates & Scaling Summary

### What Was Done

Applied rolling updates and scaling operations to the existing Kubernetes deployment by completing 4 tasks in order:

1. **Scale Replicas** — scaled the deployment to adjust the number of running pods
2. **Restart Rollout** — performed a rolling restart of the deployment with zero downtime
3. **Update ConfigMap** — edited the ConfigMap to change `KEY: value` to `KEY: updatedvalue`
4. **Undo Rollout** — rolled the deployment back to its previous version

---

## Obstacles Encountered

### Obstacle 1: Pods Stuck in Pending State After Rollout Restart

**Problem:** After restarting the rollout, new pods were created but remained stuck in `Pending` status for over 20 minutes. Running `kubectl describe pod` revealed the following error:

```
Warning FailedScheduling — 0/2 nodes are available: 
Too many pods. No new claims to deallocate.
```

The nodes were at full capacity because old pods had not been terminated yet, leaving no room for new ones to schedule.

**Solution:** Manually deleted the stuck pending pods to free up node capacity, allowing the remaining pods to schedule properly.

---

### Obstacle 2: Endless Pod Creation Loop

**Problem:** Every time a pending pod was deleted, Kubernetes automatically created a new one to replace it. This caused an endless cycle where pods kept getting created and staying stuck in `Pending` — the cluster could never settle back down to 2 running pods.

**Root Cause:** The deployment still had an active rollback in progress, so Kubernetes kept trying to maintain the desired replica count by spinning up new pods, even though the nodes had no room.

**Solution:** Broke the cycle by scaling the deployment down to 0 replicas first, which terminated all pods and cleared the nodes completely. Then scaled back up to 2 replicas, giving Kubernetes a clean slate to schedule exactly 2 fresh pods with no conflicts.

```bash
kubectl scale deployment my-dep -n demo --replicas=0
kubectl scale deployment my-dep -n demo --replicas=2
```

---

## Final Result

- Deployment successfully scaled
- Rollout restarted with zero downtime
- ConfigMap updated from `value` to `updatedvalue`
- Rollout successfully undone and deployment stabilized
- 2 pods running cleanly after resolving node capacity issues
