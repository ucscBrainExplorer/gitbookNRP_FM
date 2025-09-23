---
description: >-
  Now that your environment is ready, let's see if you can process a single
  dataset through the UCE pipeline!
icon: window
---

# Run a Single-UCE Job

**Key files in the UCE-Experiments folder:**

* `fixed_pipeline_new.py` – Python script that downloads, preprocesses, and uploads results.
* `job_template.yaml` – Kubernetes job template with placeholders.
* `job.yaml` – The actual job file generated from the template.
* `submit_jobs.py` – Script to automatically generate and submit jobs.
* `dataset_ids.txt` – List of dataset IDs to process.

Steps:

1. Find your job.yaml file
2.  Look at line 4 of the code, where it says "name: uce-job-d6505c89"

    <figure><img src="../.gitbook/assets/Screenshot 2025-09-23 at 11.40.04 AM.png" alt=""><figcaption></figcaption></figure>

**Make the following change:** replace the last 8 characters with the first 8 characters of your dataset ID that you want to run.&#x20;

For example, if I want to run the following dataset: cff99df2-4904-44f7-9173-ff837f95606e, I would replace d6505c89 with **cff99df2 (see image below)**

<figure><img src="../.gitbook/assets/Screenshot 2025-09-23 at 11.49.25 AM.png" alt=""><figcaption></figcaption></figure>

3.  Now locate line 35, where it says "python3.10 fixed\_pipeline\_new.py..." etc

    <figure><img src="../.gitbook/assets/Screenshot 2025-09-23 at 11.44.25 AM.png" alt=""><figcaption></figcaption></figure>

**Make the following change:** after it says id="...", replace the Dataset ID with the one you are testing.&#x20;

For example, I would replace d6505c89-c43d-4c28-8c4f-7351a5fd5528, with **cff99df2-4904-44f7-9173-ff837f95606e (see image below).**

<figure><img src="../.gitbook/assets/Screenshot 2025-09-23 at 11.50.38 AM.png" alt=""><figcaption></figcaption></figure>

<mark style="color:$danger;background-color:$danger;">**WARNING: Please make sure the format does NOT change. The only parts that should be changing are the name (line 4) and the argument (line 35), and it should be a simple copy-paste into the code.**</mark>

4. Now head back to your Terminal prompt. **MAKE SURE YOU ARE IN THE CORRECT BASE and IN THE FOLDER. Note: the UCE-Experiments folder is just under my username .**&#x20;
5. Submit the job with this command: **kubectl apply -f job.yaml -n braingeneers. (see image below)**

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

It should say immediately **job.batch/uce-job-\[yourdatasetname] created**&#x20;

6. Monitor the logs:
   1. To see all the jobs running, enter: **kubectl get jobs -n braingeneers**
   2. To see all the pods, enter: **kubectl get pods -n braingeneers**
      1. From here, look at the pod created under your job (ex. it could be uce-job-cff99df2-aabb)
      2. To see the logs, enter: **kubectl logs -f \[POD\_NAME] -n braingeneers**
7. When the job is finished, you should see something like this next to the name of your job.![](<../.gitbook/assets/Screenshot 2025-09-23 at 12.08.01 PM.png>)
8. Go to a new Terminal window/tab, and activate the[ virtual environment we set up](../getting-started/local-setup-python-venv-+-aws-credentials-and-packages.md) (if it's not already up).&#x20;
9. Run this command in your virtual environment: \
   &#xNAN;_**aws s3 --profile braingeneers ls s3://braingeneersdev/uce-output-files/**_

<figure><img src="../.gitbook/assets/Screenshot 2025-09-23 at 12.10.47 PM.png" alt=""><figcaption></figcaption></figure>

10. Locate your dataset ID. Using my example, I found **cff99df2-4904-44f7-9173-ff837f95606e**

    <figure><img src="../.gitbook/assets/Screenshot 2025-09-23 at 12.11.57 PM.png" alt=""><figcaption></figcaption></figure>

Note: If the dataset has more than 5,000 cells, it should be splitting it into batches of 15,000 cells per batch to save time. To make sure that all of the batches line up with how many cells the dataset contains, just do simple math:

ex. For cff99df2, there is a total of 29 batches in the bucket (batch0 to batch28). Dataset cff99df2 contains 424528 cells and the chunking size is 15000 cells. To calculate:&#x20;

* Do 424528/15000, which equals 28.3018667. Always round up (decimal values mean there is enough for another batch but not exactly 15000). Therefore, there should be 29 batches total.&#x20;
* Is there 29 batches for this set? Yes!
* If it stopped at a certain batch #, go to [resume-from-a-specific-batch.md](resume-from-a-specific-batch.md "mention") to learn what to do.\


