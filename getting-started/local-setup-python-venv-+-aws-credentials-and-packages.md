---
description: >-
  Now, you will need a Python virtual environment (venv) and an AWS profile to
  read and write data in S3.
icon: pen-to-square
---

# Local Setup: Python venv + AWS (Credentials & Packages)

### Pre-Step: Using Python 3.10-3.12

Before creating the project environment, lets make sure your computer has a Python version between 3.10-3.12 only. These versions have been tested and work properly with the pipeline and package sets.&#x20;

### 1. Check your current version

```
python3 --version
```

<mark style="background-color:green;">If it shows 3.10.x, 3.11.x, or 3.12.x, you're good.</mark>

<mark style="background-color:red;">If not in this range: install 3.12 (recommended)</mark>

*   Mac (Homebrew)

    ```
    brew install python@3.12
    brew link python@3.12
    ```
*   Linux (Ubuntu/Debian)

    ```
    sudo apt update
    sudo apt install python3.12 python3.12-venv
    ```
* &#x20;Windows - Download the 3.12 Installer from [python.org](https://www.python.org/downloads/release/python-3120/)

Once you have Python 3.10–3.12, continue with the virtual environment setup below.

### Step 1 – Create a Python Virtual Environment

A virtual environment (venv) keeps the project's Python packages isolated.&#x20;

```
python3 -m venv ~/myvenv
source ~/myvenv/bin/activate
python -V #confirm it matches 3.10-3.12
pip install --upgrade pip wheel setuptools # this will ___
```

When the venv is active, you'll see (myvenv) in your terminal prompt.&#x20;

### Step 2 - Install Core Packages

Install the AWS CLI and scientific packages inside the venv:&#x20;

```
pip install awscli==1.36.17 boto3 s3fs anndata scanpy pandas numpy scipy h5py
```

_Why pin awscli==1.36.17?_ It matches the version used in the cluster environment and it will avoid authentication mismatches.&#x20;

### Step 3 - Configure AWS Credentials

You need AWS credentials so your computer can read and write to the Braingeneers S3 bucket.

#### Option A - If your PI/boss gave you the files

Sometimes your supervisor will provide the two ready-made files:

* \~/.aws/credentials
* \~/.aws/config

If you received these:

1. Make sure the hidden .aws folder exists:

```
mkdir -p ~/.aws
```

2. Place the files exactly as given into that folder:

```
~/.aws/credentials
/~.aws/config
```

2. Verify the permissions are correct (readable by you only):

```
chmod 600 ~/.aws/credentials ~/.aws/config
```

That's it! You can skip running aws configure.

#### Option B - If you did not receive the files

Run the AWS CLI configuration command to create them:

```
aws configure --profile braingeneers
```

Enter:

* Access Key ID
* Secret Access Key
* Region - us-west-2
* Output format - json (or your preference)

This creates two files in \~/.aws/ automatically.

Example layout:

```
[braingeneers]
aws_access_key_id=AKIA...
aws_secret_access_key=...

[profile braingeneers]
region=us-west-2
output=json
```

### Step 4 - Quick S3 Test

Confirm everything works by listing the Braingeneers bucket:

```
aws s3 ls s3://braingeneersdev/ --profile braingeneers
```

If you see a list of files, your AWS profile is ready!

Questions? Contact us! @\_\_\_\_
