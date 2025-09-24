---
description: You'll use Kubectl to talk to the Nautilus cluster from your computer.
icon: pen-to-square
---

# NRP: Cluster Access via Kubectl (Kubeconfig, kubelogin)

### What is `kubectl?`

kubectl is a command-line tool that controls a Kubernets cluster. Think of it as the remote control for your cloud environment; you'll use it to:&#x20;

* Run jobs
* Check pods and nodes
* Manage resouces (files, storage, and containers)

### 1. [Install](https://kubernetes.io/docs/tasks/tools/) kubectl

Click on the link depending on your laptop's operating system.&#x20;

* [MacOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/) (latest version is fine)
* [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
* [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)

### 2. [Install the kubelogin plugin](https://github.com/int128/kubelogin?tab=readme-ov-file#setup)

You **must** install the kubelogin plugin or your kubeconfig file will not work.

**Using Kubectl in Windows Subsystem for Linux (WSL)?**

* Authentication can fail unless you add a few arguments:

<mark style="background-color:red;">**‼️ Problem A: Browser won't open**</mark>

<mark style="background-color:green;">✅ Solution: Add</mark>

*   ```
    --browser-command="/mnt/c/Program Files/Google/Chrome/Application/chrome.exe"
    ```

    to open Chrome from Windows.

<mark style="background-color:red;">**Problem B: Port 8000 already in use or fails to bind:**</mark>&#x20;

<mark style="background-color:green;">**Solution: Add**</mark>&#x20;

```
--listen-port=18000
```

**Problem C:** Keyring write error (e.g. /run/user/1000/bus not found):&#x20;

Solution: Add the following to store the token on disk instead of using the Linux keyring.

```
 --token-cache-storage=disk
```

Example config file:

```
args:
  - oidc-login
  - get-token
  - --browser-command="/mnt/c/Program Files/Google/Chrome/Application/chrome.exe"
  - --listen-port=18000
  - --token-cache-storage=disk
  - ...
```

For more context and discussion, see [this related GitHub issue](https://github.com/int128/kubelogin/issues/535).

**Console-only login (no-browser)**

* If you only have a Linux console, you can use device flow. **Add these arguments to the config file:**

```
args:
  - oidc-login
  - get-token
  - --grant-type=device-code
  - --skip-open-browser
  - ...
```

### 3. Save the Kubeconfig&#x20;

1. [Download Config File](https://nrp.ai/config)
2.  Create the config folder if it doesn't exist:

    ```
    mkdir ~/.kube
    ```
3. Save the Nautilus config on your  `~/.kube/config` (no extension)

### 4. Make sure you are using the correct config file.

*   If you have access to multiple clusters, select Nautilus:&#x20;

    ```
    kubectl config use-context nautilus
    ```

You can also run the following command to list available Kubernetes contexts:

```
$ kubectl config get-contexts
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
*         nautilus   nautilus   oidc       braingeneers
```

*   Set your default namespace (replace with braingeneers):&#x20;

    ```
    kubectl config set contexts.nautilus.namespace <YOUR NAMESPACE>
    ```

### 5. Test your connection by running the following

```
kubectl get pods -n <YOUR_NAMESPACE>
```

You should see a list that pops up of all the current pods that are running on the namespace.&#x20;

If you see “No resources found in your namespace”, don't panic, that's still good! It means you can reach the cluster, but no pods are running yet.

### If you reached the end of this page successfully...CONGRATS! You are officially set up to use the Cluster!

\


