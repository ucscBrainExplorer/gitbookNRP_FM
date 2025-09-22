---
description: Welcome to the UCE Project and Nautilus User Guide
icon: hand-wave
---

# Hello!

We're part of the UCSC Genomics Institute, and this site helps you get started with our UCE Project, a huge effort to organize and understand human cells using powerful computing tools. Follow the pages in order the first time, then jump to whatever you need!

### What is UCE?

The **Universal Cell Embedding (UCE)** is a map of **36 million cells** from human tissues and diseases. It uses machine learning to group and understand cells in a unified manner, essentially assigning a _“GPS location”_ to each cell in biology.\
👉 [Read the UCE research paper](https://www.biorxiv.org/content/10.1101/2023.11.28.568918v2)

### What are we doing with the UCE Project?

At the UCSC Genomics Institute, we are focusing on **running new cell datasets through the UCE model using Nautilus,** a compute cluster on National Research Platform (NRP).

We take raw single-cell data, process it on Nautilus, and generate embeddings that let us compare new cells against millions of reference cells already in the UCE.\
This helps researchers quickly find patterns, similarities, and differences in cell types across tissues and diseases.

### Why Nautilus?

**Nautilus** is a powerful cluster of computers built for open science. It gives us free access to thousands of GPUs so we can train big models and explore cell data faster than ever.\
👉 [Learn more about Nautilus](https://nrp.ai/documentation/userdocs/tutorial/introduction/)

### Goal: Set up a working environment to run UCE jobs on Nautilus, handle large single-cell datasets (often > 100k cells) by chunking, monitor & debug pods, and store results in S3 bucket.

This guide will show you how to:

1. Get cluster access and set up **kubectl**
2. Set up local Python & AWS environments (a two-world synchronity).&#x20;
3. Ensure AWS CLI & Python environment alignment
4. Run **one** UCE job end-to-end from a template
5. Scale to many datasets with the automation script
6. Resume failed runs from a specific batch
7. Monitor, debug, and fix resources.
8. Ship and verify outputs in S3.
9. Learn useful terminology.

\
You can learn how to use it by following the simple guides in this GitBook.
