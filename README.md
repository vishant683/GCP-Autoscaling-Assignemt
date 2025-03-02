# GCP-Autoscaling-Assignment
Overview

This project provides step-by-step instructions to set up a virtual machine (VM) on Google Cloud Platform (GCP), configure auto-scaling policies, and implement security measures such as IAM role management and firewall rules.

Features

VM Instance Creation: Deploys a virtual machine on GCP.

Auto-Scaling Setup: Configures an instance template and a managed instance group (MIG) to dynamically scale based on CPU utilization.

Security Configuration: Implements IAM roles and firewall rules to control access and secure the VM.

Prerequisites

A Google Cloud account with billing enabled.

Installed Google Cloud SDK (gcloud CLI).

Proper IAM permissions to create instances and configure networking.

**Setup Instructions**

**1. Authenticate with GCP**

gcloud auth login
gcloud config set project <YOUR_PROJECT_ID>

Replace <YOUR_PROJECT_ID> with your actual GCP project ID.

**2. Create a Virtual Machine**

gcloud compute instances create vishant-instance \
  --zone=us-east1-c \
  --machine-type=e2-medium \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --tags=http-server,https-server \
  --boot-disk-size=50GB

**3. Configure Auto-Scaling**

**Create an Instance Template****

gcloud compute instance-templates create vishant-instance-template \
  --machine-type=e2-medium \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --boot-disk-size=50GB \
  --tags=http-server,https-server

**Create a Managed Instance Group (MIG)**

gcloud compute instance-groups managed create vishant-instance-group \
  --base-instance-name=vishant-instance \
  --size=1 \
  --template=vishant-instance-template \
  --zone=us-east1-c

**Set Auto-Scaling Policy**

gcloud compute instance-groups managed set-autoscaling vishant-instance-group \
  --zone=us-east1-c \
  --min-num-replicas=1 \
  --max-num-replicas=5 \
  --target-cpu-utilization=0.6 \
  --cool-down-period=90

****4. Security Configuration**

**Assign IAM Roles****

gcloud projects add-iam-policy-binding <YOUR_PROJECT_ID> \
  --member=user:<YOUR_EMAIL> \
  --role=roles/compute.instanceAdmin

gcloud projects add-iam-policy-binding <YOUR_PROJECT_ID> \
  --member=user:<YOUR_EMAIL> \
  --role=roles/viewer

**Configure Firewall Rules**

gcloud compute firewall-rules create allow-traffic-rules \
  --direction=INGRESS \
  --priority=1000 \
  --network=default \
  --action=ALLOW \
  --rules=icmp,tcp:80,tcp:443,udp:67-69 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=http-server,https-server

**5. Stress CPU to Trigger Auto-Scaling**

**To stress the CPU and trigger an additional VM instance:**

sudo apt update && sudo apt install -y stress
stress --cpu 4 --timeout 300

**Monitor scaling:**

gcloud compute instance-groups managed list-instances vishant-instance-group --zone=us-east1-c

**Architecture Diagram**
![output (1)](https://github.com/user-attachments/assets/a5c4a95d-41b3-448a-bddb-cb4eb92da858)


This setup consists of:

A VM instance in GCP.

A Managed Instance Group (MIG) handling auto-scaling.

IAM roles restricting access to the VM.

Firewall rules securing network access.
