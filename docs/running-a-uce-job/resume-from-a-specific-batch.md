---
icon: window
---

# Checking Outputs

### **Debugger pod:**

The debugger pod at k8s/shared-uce-data-pvc-debugger.yaml provides a shell environment with access to the input/output PVCs.

### **It's useful for:**

* Inspecting output files
* Running analysis scripts
* Debugging file issues
* Checking disk space

### Option 1: Check S3 (for uploaded outputs)

**aws s3 --profile braingeneers ls s3://braingeneersdev/uce-output-files/**

Find your dataset's output folder. Outputs are organized by dataset ID.

### Option 2: Check PVC (for local outputs)\\

1. Check to see debugger pod is running

```
kubectl get pods -n braingeneers
```

Look for: shared-uce-data-pvc-debugger

If it's not there, you will need to restart it

* kubectl apply -n braingeneers -f k8s/shared-uce-data-pvc-debugger.yaml
* kubectl wait --for=condition=Ready pod/shared-uce-data-pvc-debugger -n braingeneers --timeout=300s

If it's there, move on to checking for outputs.&#x20;

**For UCE:**&#x20;

kubectl exec shared-uce-data-pvc-debugger -n braingeneers -- \ sh -lc 'ls -lh /data/output/UCE/\*uce\*.h5ad'

**Scimilarity outputs**

kubectl exec shared-uce-data-pvc-debugger -n braingeneers -- \ sh -lc 'ls -lh /data/output/scimilarity/\*scimilarity\*.h5ad'<br>

**For large datasets with multiple chunks:**

List all chunks for a specific dataset

kubectl exec shared-uce-data-pvc-debugger -n braingeneers -- \ sh -lc 'ls -lh /data/output/UCE/\*\<dataset-id>\*\_cell\_\*\_uce\_adata.h5ad'<br>

<br>



<br>

<br>
