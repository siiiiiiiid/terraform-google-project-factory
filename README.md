Cloud Foundation Toolkit Lab - 00-Setup
0. Prerequisite
A GCP Project linked with a valid biiling account
Project IAM Admin (roles/resourcemanager.projectIamAdmin) for your GCP account
1. What You'll Learn
Google Cloud SDK
Cloud Shell
Enable Google Cloud API
IAM Roles
Terraform GCP Provider Credential
2. Google Cloud SDK
2.1 Install Google Cloud SDK
You can skip this step, if using Cloud Shell

Go to Google Cloud SDK and download interactive installer for your platform.

2.2 Setup your training environment
01 - Environment Variables

Replace YOUR_PROJECT_ID with the GCP Project ID you are using for the training
Replace YOUR_GCP_ACCOUNT_EMAIL with your GCP account
SERVICE_ACCOUNT is the GCP Service Account to run Terraform with
export PROJECT_ID=YOUR_PROJECT_ID
export GCP_ACCOUNT_EMAIL=YOUR_GCP_ACCOUNT_EMAIL
export SERVICE_ACCOUNT=cft-training@${PROJECT_ID}.iam.gserviceaccount.com
02 - Verify the environment variables are set

echo "Project: ${PROJECT_ID}"
echo "GCP Account: ${GCP_ACCOUNT_EMAIL}"
echo "Service Account: ${SERVICE_ACCOUNT}"
03 - Setup & Verify gcloud configurations for the training

gcloud config configurations create cft-training
gcloud config set project ${PROJECT_ID}
gcloud config set account ${GCP_ACCOUNT_EMAIL}
gcloud config list
04 - Login and authorize gcloud to access GCP with your training user credential

gcloud auth login
3. Prepare you training GCP Project
3.1 Google Cloud APIs
Note: Skip this section if you are attending an organized training session.

You will need to enable the following Google Cloud APIs on the training project

Cloud Resource Manager API
Identity and Access Management (IAM) API
gcloud services enable cloudresourcemanager.googleapis.com
gcloud services enable iam.googleapis.com
3.2 IAM roles
Note: Skip this section if you are attending an organized training session.

You will need add the following IAM policy binding to your training project

Service Usage Admin roles/serviceusage.serviceUsageAdmin
Service Account Admin roles/iam.serviceAccountAdmin
Service Account Key Admin roles/iam.serviceAccountKeyAdmin
Storage Admin roles/storage.admin
Logging Admin logging.admin
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member="user:${GCP_ACCOUNT_EMAIL}" --role="roles/serviceusage.serviceUsageAdmin"
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member="user:${GCP_ACCOUNT_EMAIL}" --role="roles/iam.serviceAccountAdmin"
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member="user:${GCP_ACCOUNT_EMAIL}" --role="roles/iam.serviceAccountKeyAdmin"
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member="user:${GCP_ACCOUNT_EMAIL}" --role="roles/storage.admin"
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member="user:${GCP_ACCOUNT_EMAIL}" --role="roles/logging.admin"
3.3 Service Account
Note: Skip this section if you are attending an organized training session.

Following the least privilidge principle, create a separate Service Account to run Terraform with.

Create Service Account
gcloud iam service-accounts create cft-training --description="CFT Training Terraform Service Account" --display-name="CFT Training"
Verify Service Account
gcloud iam service-accounts list --filter="EMAIL=${SERVICE_ACCOUNT}"
Grant IAM Role
Grant IAM roles to the Service Account

Project IAM Admin
Storage Admin
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member="serviceAccount:${SERVICE_ACCOUNT}" --role="roles/resourcemanager.projectIamAdmin"
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member="serviceAccount:${SERVICE_ACCOUNT}" --role="roles/storage.admin"
4. Prepare Terraform Credential
Make sure you are in the cloud-foundation-training repository root directory

Service Account key
Create and download a service account key for Terraform

gcloud iam service-accounts keys create cft-training.json --iam-account=${SERVICE_ACCOUNT}
Setup Terraform Credential
Supply the key to Terraform using the environment variable GOOGLE_CLOUD_KEYFILE_JSON, setting the value to the location of the file.

export GOOGLE_CLOUD_KEYFILE_JSON="$(pwd)/cft-training.json"
