---
description: Now that you're able to run a single uce-job, let's automate it!
icon: window
---

# Automate Many Datasets

To run many datasets at once:

1. Put all dataset IDs in `dataset_ids.txt` (one per line).
2.  Run the submit script:

    python submit\_jobs.py --apply

This reads each ID, creates a job file, and submits it.

**Tips:**

* Limit how many jobs you start at once to avoid overloading the cluster.
* Use descriptive job names that include the dataset ID for easy tracking.
