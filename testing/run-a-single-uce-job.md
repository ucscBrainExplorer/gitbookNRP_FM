---
description: >-
  Now that your environment is ready, let's see if you can process a single
  dataset through the UCE pipeline!
icon: window
---

# Run a Single-UCE Job

**Key files in the UCE-Experiments folder:**

* `fixed_pipeline.py` – Python script that downloads, preprocesses, and uploads results.
* `job_template.yaml` – Kubernetes job template with placeholders.
* `job.yaml` – The actual job file generated from the template.
* `submit_jobs.py` – Script to automatically generate and submit jobs.
* `dataset_ids.txt` – List of dataset IDs to process.

Steps:

1. Copy `job_template.yaml` to `job.yaml` and fill in:

* Dataset ID
* Resource limits (memory, GPU)
* Environment variables like `CHUNK_SIZE`

2. Submit the job:\
   kubectl apply -f job.yaml -n \<YOUR\_NAMESPACE>
3. Watch the logs:\
   kubectl get pods -n \<YOUR\_NAMESPACE> \
   kubectl logs -f \<POD\_NAME> -n \<YOUR\_NAMESPACE>

When finished, go to a new terminal tab, and activate the[ virtual environment we set up](../getting-started/local-setup-python-venv-+-aws-credentials-and-packages.md). Then run this command: \
&#xNAN;_**aws s3 --profile braingeneers ls s3://braingeneersdev/uce-output-files/**_\
and check the bucket to see if your \


* Download a file to inspect:\
  aws s3 cp s3://braingeneersdev/uce-output-files/\<DATASET\_ID>\_batch0\_uce\_adata.h5ad ./ --profile braingeneers\


