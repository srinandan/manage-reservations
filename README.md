# Application Integration: Manage Reservations Sample

This sample repository includes a sample for [Application Integration](https://cloud.google.com/application-integration/docs/overview).

# Description

TODO

# Prerequisites

* The integration version JSON is downloaded [here](./src/executeworkflows.json)
* This repo uses a custom cloud builder called `integrationcli-builder`. , based on [integrationcli](https://github.com/srinandan/integrationcli) and gcloud

# Configuring Cloud Build

In the settings page of Cloud Build enable the following service account permissions:
* Secret Manager (Secret Manager Accessor)
* Service Accounts (Service Account User)
* Cloud Build (Cloud Build WorkerPool User)

Get the project number:

```
PROJECT_NUMBER=$(gcloud projects describe $(gcloud config get-value project) --format="value(projectNumber)" | xargs)
```

Grant the Application Integration Admin role to the Cloud Build Service Agent

```
    gcloud projects add-iam-policy-binding $(gcloud config get-value project) \
        --member="serviceAccount:service-$PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/integrations.integrationAdmin"
```

Grant the Connector Admin role to the Cloud Build Service Agent

```
    gcloud projects add-iam-policy-binding $(gcloud config get-value project) \
        --member="serviceAccount:service-$PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/connectors.admin"
```

Grant the Secret Manager Admin role to the Cloud Build Service Agent (to create the secret manager version)

```
    gcloud projects add-iam-policy-binding $(gcloud config get-value project) \
        --member="serviceAccount:service-$PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/secretmanager.admin"
```

Grant the Cloud KMS Decryptor role to the Cloud Build Service Agent (to decrypt the database password)

```
    gcloud projects add-iam-policy-binding $(gcloud config get-value project) \
        --member="serviceAccount:service-$PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/cloudkms.cryptoKeyDecrypter"
```

Grant the Service Account User role to the Cloud Build Service Agent (to access other SAs)

```
    gcloud projects add-iam-policy-binding $(gcloud config get-value project) \
        --member="serviceAccount:service-$PROJECT_NUMBER@gcp-sa-cloudbuild.iam.gserviceaccount.com" \
        --role="roles/iam.serviceAccountUser"
```


## Steps

1. Modify the [cloudbuild](./cloudbuild.yaml) file to set the appropriate
   a. Service Account with permissions to execute a Workflow
   b. Cloud KMS key to decrypt

```
#the name of the service account  to use when setting up the connector
substitutions:
  _SERVICE_ACCOUNT_NAME: "<sa-name>"
  _KMS_RING_NAME: "<ring-name>"
  _KMS_KEY_NAME: "<key-name>"
```
Ensure this service account has roles/cloudsql.editor permission.

3. Trigger the build manually

```sh

gcloud builds submit --config=cloudbuild.yaml --region=<region-name> --project=<project-name>
```

The integration is labeled with the `SHORT_SHA`, the first seven characters of the commit id
___

## Support

This is not an officially supported Google product
