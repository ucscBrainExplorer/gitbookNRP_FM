# 🧬 UCE Chunk Merge Pipeline

This repository automates the process of **combining distributed UCE inference outputs** into a single `.h5ad` file per dataset on the **NRP platform**.

All required components are included in the `RunFoundationModelNRP` repository:
- `combine_uce_chunks.py` — Python script to verify, merge, and write combined `.h5ad` outputs.  
- `uce-merge-job-template.yaml` — Kubernetes Job template for launching merge jobs on NRP.  
- `launch-uce-merge-jobs.sh` — Shell script to generate and launch merge jobs automatically.  
- `Dockerfile.combine_chunks` — Docker image definition for the merge environment.

---

## 🚀 Usage Guide

### 1️⃣ Edit the list of datasets

Open the file **`launch-uce-merge-jobs.sh`** and modify the `datasets` array to include the dataset IDs you want to recombine:

```bash
datasets=(
  ucsc-2e934674-0669-4d8a-9faa-9d37078f8bb6
  13149914-ea03-4f01-8bf6-b793b667127b
  8e10f1c4-8e98-41e5-b65f-8cd89a887122
)
```

Each dataset ID should correspond to the prefix of your chunked `.h5ad` files in `/data/output/UCE/`.

---

### 2️⃣ Make the launch script executable

Run this once to ensure you can execute the script:

```bash
chmod +x launch-uce-merge-jobs.sh
```

---

### 3️⃣ Launch the merge jobs

Run the script:

```bash
./launch-uce-merge-jobs.sh
```

This will:
1. Generate a temporary job YAML for each dataset (based on `uce-merge-job-template.yaml`).
2. Apply each job to Kubernetes (`kubectl apply`).
3. Automatically delete the temporary YAMLs after submission.

---

### 4️⃣ Monitor progress

Check job status:

```bash
kubectl get jobs
kubectl get pods
```

Follow logs for a specific dataset:

```bash
kubectl logs -f job/uce-merge-job-<DATASET_ID>
```

---

### 5️⃣ Verify and collect outputs

Once complete, each merged `.h5ad` will appear under:

```
/data/output/combined_UCE/
```

To copy the merged file locally:

```bash
kubectl cp <pod-name>:/data/output/combined_UCE/<dataset-id>_uce_adata.h5ad .
```
(Normally, the above can be accomplished by interfacing with the debugger pod. See (this link)[https://ucsc-xena.gitbook.io/running-uce-on-nrp/running-a-uce-job/monitor-and-debug-jobs] for more...)
---

### 6️⃣ Clean up completed jobs (optional)

```bash
kubectl delete job -l job-name=uce-merge-job
```

---

## 🧩 Notes
- The PVC `shared-uce-data-output-pvc` must be mounted at `/data/output` as in the provided job spec.
- Each job runs independently — datasets are merged in parallel.
- Outputs remain safe in the PVC even if your local computer disconnects or shuts down.
- Resource requests and limits (CPU & memory) can be adjusted in `uce-merge-job-template.yaml`.

---

### ✅ Example run summary

```bash
Applying job for dataset: 8e10f1c4-8e98-41e5-b65f-8cd89a887122
job.batch/uce-merge-job-8e10f1c4-8e98-41e5-b65f-8cd89a887122 created
Deleted temporary file: job-8e10f1c4-8e98-41e5-b65f-8cd89a887122.yaml
All jobs applied and temporary YAMLs removed.
```

---

**That’s it!**  
Modify → `chmod +x` → `./launch-uce-merge-jobs.sh` → wait for merges → retrieve from `/data/output/combined_UCE/`.
