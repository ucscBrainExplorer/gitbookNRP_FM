---
icon: window
---

# Building your own Docker Image

To build your own Docker Image, here's some things to do first:&#x20;

Prerequisites:

*   **Goal:** make sure your laptop and registry are ready so you can build _and_ push images the cluster can run.



### Step 1: Install and verify Docker

1. **Install** Docker Desktop (macOS/Windows) or Docker Engine (Linux).
2. **Start** Docker Desktop (little whale icon) so the icon is running.
3. **Verify** in a terminal:

```bash
docker --version
docker info | head -n 15
# Quick test container
docker run --rm hello-world
```

> Linux only: if `permission denied` without `sudo`, add yourself to the docker group:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

### Step 2 - Create a Docker registry repo (Docker Hub)

1. Go to hub.docker.com → **Sign up / Sign in**.
2. Click **Create Repository** → name it, e.g. `uce-experiments`.
3. Pick **Public** (easy) or **Private** (then you’ll need imagePullSecrets on the cluster).
4. Your image name will look like: `rvsamant/uce-experiments:<tag>`.
5. **Login from CLI** so pushes work:

```bash
docker login
# enter your Docker Hub username + PAT/password; expect: "Login Succeeded"
```

### Step 3 — Ensure you have the right Dockerfile

The repository already contains a working `Dockerfile`, so you don’t need to write a new one. Instead, you will **build your own image** from this provided file and push it to **your own Docker Hub repository (ex. xx/uce-experiments, rvsamant/uce-experiments).** Just confirm that you are in the folder where the `Dockerfile` lives (usually the project root) before building.

**Step 4 — Build and push your image for linux/amd64**

**Now that you have the correct Dockerfile,** run this single command from the folder where the Dockerfile lives (it should be in the main UCE-Experiments folder that you forked from the repo)

```
docker buildx build \
--platform linux/amd64 \
-t <your-dockerhub-username>/uce-experiments:v1-amd64 \
--push \
.
```

**Tagging details:**

* Replace **`<your-dockerhub-username>/uce-experiments`** with your own Docker Hub repository name (e.g., `rvsamant/uce-experiments`, `vgutta/uce-experiments`).
* The `v1` portion is a **version tag** that you increment each time you build a new image (`v2`, `v3`, …). This helps track changes and ensures Kubernetes jobs pull the intended version.
* Keep the `-amd64` suffix to indicate the architecture.

**Explanation of flags:**

* `docker buildx build` – uses Docker Buildx (multi-platform builder).
* `--platform linux/amd64` – forces an x86\_64 build so it runs on NRP Nautilus nodes.
* `-t rvsamant/uce-experiments:v15-amd64` – names and tags your image (`username/repo:tag`). Replace `rvsamant` with your Docker Hub username and bump the tag (e.g., `v16`) when you rebuild.
* `--push` – automatically uploads the built image to Docker Hub.
* `.` – tells Docker to use the current directory as the build context (where your Dockerfile is).

Once this finishes, your personal Docker Hub repo will contain an amd64-compatible image ready for Kubernetes jobs.

### Step 5 - Update the job.yaml, job\_template.yaml

On lines 29, replace the line with your dockerhub username/uce-experiments:\[version-#],&#x20;

For example, the image below:

<figure><img src="../.gitbook/assets/Screenshot 2025-09-29 at 12.34.47 AM.png" alt=""><figcaption></figcaption></figure>

**Common directory:**

* If you see `failed to read dockerfile: open Dockerfile: no such file or directory`, you’re not in the directory that contains your `Dockerfile`.  Fix the following:

```
# Example: move into the repo folder that has Dockerfile
cd ~/UCE-Experiments
ls -la # should show Dockerfile
```

If you ever need to edit **any of the code files, make sure to:**

1. **Change the version number of the image:**&#x20;

**ex. If you are moving from v1 to v2, you have to build and push a new one such as this:**&#x20;

```
docker buildx build \
--platform linux/amd64 \
-t <your-dockerhub-username>/uce-experiments:v2-amd64 \
--push \
.
```
