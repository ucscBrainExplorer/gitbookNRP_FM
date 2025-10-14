---
description: >-
  Now that your environment is ready, let's see if you can process datasets
  through the UCE pipeline!
icon: window
---

# Run a UCE Job

This page documents how to check out the code to run a UCE pod from github, edit the dataset\_ids.txt file with the list of datasets to run, and run the code to start UCE pod(s) on NRP.



1. **Activate the**[ **virtual environment we set up**](../../getting-started/local-setup-python-venv-+-aws-credentials-and-packages.md) **(if it's not already up) and check out the UCE-Experiments code**

```
cd ~
gh repo clone ucscBrainExplorer/RunFoundationModelNRP
```

This will make a copy of the code you need to run a UCE job on your computer

Key files in the UCE-Experiments folder:

* `job_template.yaml` – a _template_ Kubernetes Job with placeholders used to generate many jobs.
* `submit_jobs.py` – reads `dataset_ids.txt`, renders jobs from `job_template.yaml`, and submits them.
* `dataset_ids.txt` – newline-separated list of dataset IDs, number of cells, and download\_source to process.



2. **Enter the UCE-experiments folder**

```
cd UCE-experiments
```

This should be just under your username folder



3. **Add your dataset ID to dataset\_ids.txt**

Edit the existing dataset\_ids.txt file. The file will contain a list of dataset ids with corresponding number of cells in the dataset as well as the source of the dataset. There are two data sources: cellxgene and UCSC. cellxgene tells the code to download the dataset from [CELLxGENE](https://cellxgene.cziscience.com/). UCSC tells the code to download the dataset from an S3 bucket run by Jing. If your dataset has more than 100,000 cells add <kbd>\_advanced</kbd> to the <kbd>download\_source</kbd> and reference the [Advanced mode documentation](advanced-mode.md).

All datasets must be on a new line. All data about a dataset must be separated by tabs.&#x20;

Delete any datasets that are already in the file and add your own. Here is an example dataset\_ids.txt file with 5 datasets, 1 of which (<kbd>8e10f1c4-8e98-41e5-b65f-8cd89a887122</kbd>) will not be run because it is commented out with a "#".

```
#dataset_id	dataset_total_cell_count	download_source
d973ea15-3722-4bc8-a0e8-1d956d26e203	1837	cellxgene
ucsc-2e934674-0669-4d8a-9faa-9d37078f8bb6	10348	UCSC
13149914-ea03-4f01-8bf6-b793b667127b	1665937	cellxgene_advanced
#8e10f1c4-8e98-41e5-b65f-8cd89a887122	2480956	cellxgene_advanced
ucsc-b165f033-9dec-468a-9248-802fc6902a74	888263	UCSC_advanced
```

{% hint style="info" %}
Adding a "#" to the beginning of the line in dataset\_ids.txt will skip processing that dataset.
{% endhint %}

{% hint style="danger" %}
If your dataset has more than 100,000 cells please use [Advanced mode](advanced-mode.md)
{% endhint %}

4. **Edit submit\_jobs.py**

Open <kbd>submit\_jobs.py</kbd> in your favorite text editor. We recommend [nano](https://www.geeksforgeeks.org/linux-unix/nano-text-editor-in-linux/) if you do not have one. If you use textedit or similar, make sure that the formatting of the file does not change. Do not use Microsoft Word or similar as it will change the formatting of the file.

There are two key values in submit\_jobs.py

* <kbd>start\_cell</kbd> which tells the code where to start the <kbd>chunk\_size</kbd>. This will be <kbd>0</kbd> unless you are restarting a failed job, in which case you will start with the last correctly processed cell number
* <kbd>max\_pod\_number</kbd> which tells the code how many pods to use for all of the datasets in dataset\_ids.txt. You will need at least one pod for every dataset in the dataset\_ids.txt file. Please see [Advanced mode documentation](advanced-mode.md) for setting the <kbd>max\_pod\_number</kbd> for the advanced mode.

{% hint style="info" %}
If you do not set max\_pod\_number high enough, not all the datasets will be processed.&#x20;
{% endhint %}



5. **Submit the jobs to NRP**

```
python submit_jobs.py 
```

It should say immediately <kbd>job.batch/uce-job-\[yourdatasetname] created</kbd>&#x20;



6. **Monitor the logs:**

```
kubectl get jobs -n braingeneers
```

This command will list all the pods that are under the braingeneers namespace. Look for pod created under your job (ex. it could be uce-job-cff99df2-aabb)

```
kubectl logs -f <POD_NAME> -n braingeneers
```

This command will show the logs for the pod listed. This will show you if there were any errors in the pipeline.

When the job is finished, you should see something like this next to the name of your job. ![](<../../.gitbook/assets/Screenshot 2025-09-23 at 12.08.01 PM.png>)



7. **Check that the output files are in the correct place**

Go to a new Terminal window/tab, and activate the[ virtual environment we set up](../../getting-started/local-setup-python-venv-+-aws-credentials-and-packages.md) (if it's not already up). Run this command in your virtual environment

```
aws s3 --profile braingeneers ls s3://braingeneersdev/uce-output-files/
```

Locate your dataset ID to verify that the output files are there.
