---
icon: window
---

# Monitor and Debug Jobs

**Common Commands**

```
# list all jobs in the braingeneers namespace and show their status (Active/Succeeded/Failed)
kubectl get jobs -n braingeneers        

# list all pods in the braingeneers namespace and show current state (Pending/Running/Completed)
kubectl get pods -n braing

# detailed info for a specific pod: events, resource usage, reason for restarts, error messages
kubectl describe pod <POD> -n braingeneers

# stream live logs from the container in real time (Ctrl-C to stop)
kubectl logs -f <POD> -n braingeneers
```

more incoming..
