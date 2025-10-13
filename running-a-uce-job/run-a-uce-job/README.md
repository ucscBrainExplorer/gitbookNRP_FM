---
description: >-
  Now that your environment is ready, let's see if you can process a single
  dataset through the UCE pipeline!
icon: window
---

# Run a UCE Job

**Key files in the UCE-Experiments folder:**

* `fixed_pipeline_new.py` – Python script that downloads, preprocesses, and uploads results.
* `job_template.yaml` – a _template_ Kubernetes Job with placeholders used to generate many jobs.
* `job.yaml` – a _concrete_ Kubernetes Job used to run **one** dataset ID.
* `submit_jobs.py` – reads `dataset_ids.txt`, renders jobs from `job_template.yaml`, and submits them.
* `dataset_ids.txt` – newline-separated list of dataset IDs to process.

### Option A: Run a **single** dataset using `job.yaml`

Use this when you want to test or re-run **one** dataset by hand.

**What to edit (two places only)**

1. Find your job.yaml file
2.  Look at line 4 of the code, where it says "name: uce-job-d6505c89"

    <figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 11.40.04 AM.png" alt=""><figcaption></figcaption></figure>

**Make the following change:** replace the last 8 characters with the first 8 characters of your dataset ID that you want to run.&#x20;

For example, if I want to run the following dataset: cff99df2-4904-44f7-9173-ff837f95606e, I would replace d6505c89 with **cff99df2 (see image below)**

<figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 11.49.25 AM.png" alt=""><figcaption></figcaption></figure>

3.  Now locate line 35, where it says "python3.10 fixed\_pipeline\_new.py..." etc

    <figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 11.44.25 AM.png" alt=""><figcaption></figcaption></figure>

**Make the following change:** after it says id="...", replace the Dataset ID with the one you are testing.&#x20;

For example, I would replace d6505c89-c43d-4c28-8c4f-7351a5fd5528, with **cff99df2-4904-44f7-9173-ff837f95606e (see image below).**

<figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 11.50.38 AM.png" alt=""><figcaption></figcaption></figure>

<mark style="color:$danger;background-color:$danger;">**WARNING: Please make sure the format does NOT change. The only parts that should be changing are the name (line 4) and the argument (line 35), and it should be a simple copy-paste into the code.**</mark>

4. Now head back to your Terminal prompt. **MAKE SURE YOU ARE IN THE CORRECT BASE and IN THE FOLDER. Note: the UCE-Experiments folder is just under my username .**&#x20;
5. Submit the job with this command: **kubectl apply -f job.yaml -n braingeneers. (see image below)**

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

It should say immediately **job.batch/uce-job-\[yourdatasetname] created**&#x20;

6. Monitor the logs:
   1. To see all the jobs running, enter: **kubectl get jobs -n braingeneers**
   2. To see all the pods, enter: **kubectl get pods -n braingeneers**
      1. From here, look at the pod created under your job (ex. it could be uce-job-cff99df2-aabb)
      2. To see the logs, enter: **kubectl logs -f \[POD\_NAME] -n braingeneers**
7. When the job is finished, you should see something like this next to the name of your job.![](<../../.gitbook/assets/Screenshot 2025-09-23 at 12.08.01 PM.png>)
8. Go to a new Terminal window/tab, and activate the[ virtual environment we set up](../../getting-started/local-setup-python-venv-+-aws-credentials-and-packages.md) (if it's not already up).&#x20;
9. Run this command in your virtual environment: \
   &#xNAN;_**aws s3 --profile braingeneers ls s3://braingeneersdev/uce-output-files/**_

<figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 12.10.47 PM.png" alt=""><figcaption></figcaption></figure>

10. Locate your dataset ID. Using my example, I found **cff99df2-4904-44f7-9173-ff837f95606e**

    <figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 12.11.57 PM.png" alt=""><figcaption></figcaption></figure>

### Option B: Run **Many Datasets** (`job_template.yaml` + `submit_jobs.py`)

Use this for automated bulk processing.

1. Create dataset\_ids.txt with one dataset ID per line:

```
cff99df2-4904-44f7-9173-ff837f95606e
74014ef8-d2d0-4cbc-8ba2-037f30753ffd
3b8b5de4-3aa1-4ac6-8890-8d03c8219981
```

2. Check job\_template.yaml Placeholders\
   Ensure it uses something like \[SHORT\_ID], \[FULL\_ID]

<figure><img src="../../.gitbook/assets/Screenshot 2025-09-29 at 1.12.13 AM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2025-09-29 at 1.12.27 AM (1).png" alt=""><figcaption></figcaption></figure>

_`{{SHORT_ID}}`_ = first 8 chars of dataset ID\
&#xNAN;_`{{FULL_ID}}`_ = full dataset ID

3. Submit All Jobs

```
python3.10 submit_jobs.py 
```

This will:&#x20;

* Reads each ID from the dataset\_id.txt
* Renders a job from the template
* Applies it with kubectl

4. Monitor the cluster by running the following commands:

```
kubectl get jobs -n braingeneers
kubectl get pods -n braingeneers
kubectl logs -f <POD_NAME> -n braingeneers
```

