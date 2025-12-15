---
icon: book-font
---

# Common Commands

### 1. Cluster Access & Setup (kubectl)

#### Use the Nautilus cluster

```bash
kubectl config use-context nautilus
```

#### Set default namespace

```bash
kubectl config set-context --current --namespace=braingeneers
```

#### Verify namespace

```bash
kubectl config view --minify | grep namespace
```

#### Test connection

```bash
kubectl get pods
```

***

### 2. Local Python + AWS Setup

#### Create & activate virtual environment

```bash
python3 -m venv ~/myvenv
source ~/myvenv/bin/activate
```

#### Upgrade tooling

```bash
pip install --upgrade pip wheel setuptools
```

#### Install required packages

```bash
pip install awscli==1.36.17 boto3 s3fs anndata scanpy pandas numpy scipy h5py
```

#### Configure AWS (if needed)

```bash
aws configure --profile braingeneers
```

#### Test S3 access

```bash
aws s3 ls s3://braingeneersdev/ --profile braingeneers
```

***

### 3. Repository Setup

#### Clone repo

```bash
gh repo clone ucscBrainExplorer/RunFoundationModelNRP
cd RunFoundationModelNRP
```

#### Create dataset list

```bash
cp dataset_ids.txt.example dataset_ids.txt
```

***

### 4. Submitting Jobs (UCE / Scimilarity)

#### UCE (default chunking = 10k)

```bash
python3 submit_jobs.py --embedding-method uce
```

#### Limit concurrent pods

```bash
python3 submit_jobs.py --embedding-method uce --max-pods 3
```

#### Custom chunk size

```bash
python3 submit_jobs.py --embedding-method uce --chunk-size 5000
```

***

#### Scimilarity

```bash
python3 submit_jobs.py --embedding-method scimilarity
```

```bash
python3 submit_jobs.py --embedding-method scimilarity --max-pods 3
```

```bash
python3 submit_jobs.py --embedding-method scimilarity --chunk-size 5000
```

***

### 5. Monitoring Jobs & Pods

#### List jobs

```bash
kubectl get jobs
```

```bash
kubectl get jobs | grep uce
kubectl get jobs | grep scimilarity
```

#### List pods

```bash
kubectl get pods
```

```bash
kubectl get pods | grep uce
kubectl get pods | grep scimilarity
```

#### Watch pods in real time

```bash
watch -n 5 'kubectl get pods | grep uce'
```

***

### 6. Logs & Debugging

#### Follow pod logs

```bash
kubectl logs -f <POD_NAME>
```

#### Logs for a job (auto-selects pod)

```bash
kubectl logs -f job/<JOB_NAME>
```

#### Previous logs (crash debugging)

```bash
kubectl logs <POD_NAME> --previous
```

#### Describe a pod (events, errors, scheduling)

```bash
kubectl describe pod <POD_NAME>
```

***

### 7. Deleting & Cleaning Up

#### Delete a job

```bash
kubectl delete job <JOB_NAME>
```

#### Delete many jobs by pattern

```bash
kubectl delete jobs $(kubectl get jobs | awk '/uce-job-de5416ef/ {print $1}')
```

#### Delete only failed jobs

```bash
kubectl delete jobs $(kubectl get jobs --field-selector=status.failed=1 -o name)
```

***

### 8. PVC Debugger Pod (Inspect Files)

#### Start debugger pod

```bash
kubectl apply -f k8s/shared-uce-data-pvc-debugger.yaml
```

#### Wait until ready

```bash
kubectl wait --for=condition=Ready pod/shared-uce-data-pvc-debugger --timeout=300s
```

#### Exec into debugger

```bash
kubectl exec -it shared-uce-data-pvc-debugger -- sh
```

***

### 9. Checking Outputs (PVC)

#### UCE outputs

```bash
kubectl exec shared-uce-data-pvc-debugger -- \
  sh -lc 'ls -lh /data/output/UCE/*uce*.h5ad'
```

#### Scimilarity outputs

```bash
kubectl exec shared-uce-data-pvc-debugger -- \
  sh -lc 'ls -lh /data/output/scimilarity/*scimilarity*.h5ad'
```

#### All chunks for a dataset

```bash
kubectl exec shared-uce-data-pvc-debugger -- \
  sh -lc 'ls -lh /data/output/UCE/*<dataset-id>*_cell_*_uce_adata.h5ad'
```

#### Check disk usage

```bash
kubectl exec shared-uce-data-pvc-debugger -- \
  sh -lc 'df -h /data/output'
```

***

### 10. Checking Outputs (S3)

#### List output bucket

```bash
aws s3 --profile braingeneers ls s3://braingeneersdev/uce-output-files/
```

***

### 11. Merging Chunks

#### Merge UCE chunks

```bash
./scripts/launch-uce-merge-jobs.sh <dataset-id>
```

#### Merge Scimilarity chunks

```bash
./scripts/launch-chunk-merge-jobs.sh scimilarity <dataset-id>
```

#### Merge multiple datasets

```bash
./scripts/launch-chunk-merge-jobs.sh scimilarity dataset1 dataset2 dataset3
```
