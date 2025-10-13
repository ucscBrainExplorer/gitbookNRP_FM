---
description: Parallelizing UCE for a faster runtime
icon: window
---

# Advanced mode

Advanced mode allows you to run large datasets faster by running UCE in a parallel fashion using multiple pods. It does this by pre-downloading the dataset to a persistent volume on NRP and then mounting the persistent volume to all pods running the UCE. Using a persistent volume allows chunks of the dataset to be run in parallel without downloading the same data multiple times, making the final runtime shorter. Additionally, the dataset only needs to be downloaded once, reducing the time needed for download and avoids download issues that can happen when downloading very large files.&#x20;

Advanced mode is recommended for datasets larger than 100,000 cells.

## Using Advanced mode

To mark a dataset for advanced mode, you will append  "\_advanced" to the entry in download\_source in the <kbd>dataset\_ids.txt</kbd> file. This means that the two options to use advanced mode are: "cellxgene\_advanced" and "UCSC\_advanced". Note that <kbd>dataset\_ids.txt</kbd>. can have a mix of advanced and non-advanced datasets in it. To run a single dataset, put only one dataset in <kbd>dataset\_ids.txt</kbd>.&#x20;

{% hint style="info" %}
You can comment out a dataset by adding "#" to the beginning of the line in <kbd>dataset\_ids.txt</kbd>. Commenting out a dataset will skip it in both the pre-processing and in running the UCE.
{% endhint %}

## Pre-downloading the dataset to the persistent volume

To add data to the persistent volume first marking a dataset for advanced mode in the dataset\_ids.txt file as noted above. Then run&#x20;

```
python submit_predownloaded.py
```

After you start the download process, run&#x20;

```
kubectl get pods -n braingeneers
```

To see what pods are running. The pod name will be <kbd>download-dataset-\{{FULL\_ID\_lowercase\}}</kbd>, where <kbd>FULL\_ID\_lowercase</kbd> is the dataset\_id in lowercase. Depending on the size of the file you may need to wait several minutes for the pod status to be marked as <kbd>Complete</kbd>.

## Verify that the data was downloaded correctly

After you have downloaded it, ensure that the data was downloaded correctly by checking that the byte size on the persistent volume matches the original source. To do this you will start up a debugger pod that is connected to the persistent volume. You will then connect to the debugger pod, which will allow you to inspect the persistent volume. After you have verified the byte size, you will delete the debugger pod.

**1. Start a 'debugger' pod with the persistent volume mounted.**

```
kubectl apply -f shared-uce-data-pvc-debugger.yaml
```

This command will start a new pod with no GPUs with the persistent volume mounted to it. You may need to wait a few minutes for the pod to start. You can run&#x20;

```
kubectl get pods -n braingeneers
```

To verify that the pod is running (ie. the status will be "Running"). The name of the pod will be <kbd>shared-uce-data-pvc-debugger</kbd>.

**2. Log onto the pod.**

```
kubectl exec -it shared-uce-data-pvc-debugger -- bash
```

This command will log onto the debugger pod and give you command line access to the pod. You will know it worked because your command line prompt should now read 'bash'

**3. Once you are logged onto the pod, go to the persistent volume.**

```
cd /data/input
```

This command will take you to the persistent volume. The persistent volume is mounted at <kbd>/data/input</kbd>.

**4. Look at the files in the directory and verify the size.**

```
ls -l
```

When run from <kbd>/data/input</kbd>, this command will show the files on the persistent volume and their sizes. You will check the file size is what it should be here.  You can also run

```
ls -lh
```

To get more human readable sizes.

**5. Exit the pod when you are done.**

```
exit
```

This command will exit the pod and bring you back to your home machine.

**6. Delete the debugging pod you're using to view and manipulate the persistent volume.**

```
kubectl delete pod shared-uce-data-pvc-debugger
```

This command will delete the debugging pod. While not strictly necessary since NRP will eventually delete this pod for you, it is polite to release the pod for other users.

### Other helpful commands

<table><thead><tr><th width="192.07421875">Command</th><th>Use</th></tr></thead><tbody><tr><td><pre><code>df -h
</code></pre></td><td>When run on a debugger pod, it will show you what persistent volumes are mounted to a pod.</td></tr><tr><td><pre><code>du -h
</code></pre></td><td>When run from <kbd>/data/input</kbd> (ie. inside the persistent volume) on a debugger pod, it will show you how much space is being used.</td></tr><tr><td><pre><code>kubectl get pvc
</code></pre></td><td>When run from your home machine, it will show the persistent volumes on NRP.</td></tr></tbody></table>

## Run the UCE

After verifying the download you can run the UCE following the steps in [Run a UCE job](advanced-mode.md#run-the-uce).

## Delete data off the persistent volume after running the UCE

You will need to delete data after it has been run through the UCE from the persistent volume so that there is space available for other people who are also running data through the UCE. The persistent volume currently has 500Gb.&#x20;

Follow the instructions in [Verify that the data was downloaded correctly](advanced-mode.md#verify-that-the-data-was-downloaded-correctly) except that at Step 4 remove the file you want with:

```
rm <filename>
```

Don't forget to delete the pod (Step 6) when you are done.
