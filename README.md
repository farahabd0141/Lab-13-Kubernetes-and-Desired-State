  HEAD
# Docker Lab: Containerizing a Three-Tier Application
**INET 4031 - Introductions to Systems**

This lab introduces Docker and Docker Compose by having you containerize a
real, multi-service application. You will package three components: Apache,
Flask, and MariaDB. These will be packaged into separate containers and wired together so they function as a complete application.

The application code and scaffolding are provided. Your job is to complete the Dockerfiles, verify the stack runs correctly, and document your work below.

> **Directions and explanations for this lab are on the repository Wiki.**
> Refer to the Wiki pages for step-by-step instructions.

---

*The sections below are for you to fill out. Replace each placeholder with your own content before submitting. Having a detailed README is the best practice for showing your work in future GitHub repositories.*

---

# Project Overview

<!-- Briefly describe what this application does in your own words.
     What problem does it solve? What does a user interact with? -->

# Prerequisites

<!-- List what needs to be installed or configured on the VM before this lab
     will work. Include Docker, Docker Compose, and anything else required. -->

# Getting Started

<!-- Explain how a new teammate would bring this stack up from a fresh clone.
     Walk through every command they need to run, in order. -->

# Configuration

<!-- Explain the .env file: what it is, what variables it contains,
     and what a teammate needs to provide that is not in this repository. -->

# Verification

<!-- Describe how to confirm the stack is running correctly.
     Reference the check script and what a passing run looks like. -->

# Feedback (Optional)

<!-- Do you have any feedback you would like to give us after completing this lab? What are some things you enjoyed? What about others that you felt was lackluster? Or maybe there was something that we missed that you'd love for us to touch on! This will help us improve the INET 4031 lab experience. We appreciate everything we can get!  -->

# Docker-Lab-Containerizing-a-Three-Tier-Application
  f380ce92d4588e42f42fe2db8a2857ec28609a66
## Lab 13 - Kubernetes Deployment

In this lab, we took the same application from Lab 12 (Apache, Flask, and MariaDB) and moved it from Docker Compose into Kubernetes using k3s. Instead of manually running containers, Kubernetes now manages everything and keeps it running.

First, we installed k3s and made sure the node was ready. Then we created a namespace called ticket-app and used a Secret to store our database credentials instead of using a .env file.

After that, we created three Kubernetes files for the database, backend, and frontend. We applied everything using:
kubectl apply -f k8s/

At first, the app had issues connecting to the database, but we fixed it by updating the Secret and restarting the pods. After that, everything started working correctly.

We verified it using:
curl http://localhost:30080/health

It returned:
{"database":"connected","status":"healthy"}

We also tested self-healing by deleting the Flask pod. Kubernetes automatically created a new one without us doing anything, which shows how it keeps the system running.

To deploy:
kubectl apply -f k8s/

Access the app:
http://<VM-IP>:30080
