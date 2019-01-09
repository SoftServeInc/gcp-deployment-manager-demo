# GKE Deployment Manager Demo

Demo project to demonstrate GCP Deployment Manager automation tool capabilities for building GKE clusters and deploying example applications.

## Getting Started

These instructions will get you a fully functional GKE Regional Cluster including deployed example applications. See deployment for notes on how to deploy the cluster.

### Prerequisites

- Google Cloud Platform Project. [Create Project](https://console.cloud.google.com/cloud-resource-manager "Create Project")
- GCloud Command Line Tool. [Download SDK](https://cloud.google.com/sdk/ "Download SDK")

## Secrets and additional settings
1. Set your project for GCloud command line tool:
```
gcloud config set project [YOUR_PROJECT_ID]
```

2. Create Image with **nginx static files** from Google Cloud Storage:
```
export PROJECT_ID=$(gcloud config get-value project)
gcloud compute images create gcp-dm-demo-drupal-nginx-static-files --source-uri https://storage.googleapis.com/gcp-dm-demo/gcp-dm-demo-drupal-nginx-static-files.tar.gz --project $PROJECT_ID
```

3. Create Compute Disk from image:
```
gcloud compute disks create nginx-static-files --image gcp-dm-demo-drupal-nginx-static-files --zone europe-west3-b
```

4. Create a service account and download JSON key ([Manual instruction](https://cloud.google.com/iam/docs/creating-managing-service-account-keys "Manual instruction")):
```
export PROJECT_ID=$(gcloud config get-value project)
gcloud iam service-accounts create gcp-dm-demo
gcloud iam service-accounts keys create ServiceAccoutKey.json --iam-account gcp-dm-demo@${PROJECT_ID}.iam.gserviceaccount.com
base64 -w 0 ServiceAccoutKey.json > Base64_SA_Key.txt
```

5. Assign `Cloud SQL Admin` Role to service account
```
export PROJECT_ID=$(gcloud config get-value project)
gcloud projects add-iam-policy-binding $PROJECT_ID --member serviceAccount:gcp-dm-demo@${PROJECT_ID}.iam.gserviceaccount.com --role roles/cloudsql.admin
```

6. **Enable** the following **API**s for your project:
  `Compute Engine API`, `Kubernetes Engine API`, `Google Cloud Deployment Manager V2 API`, `Stackdriver Logging API`, `Stackdriver Monitoring API`, `Cloud SQL Admin API`
```
gcloud services enable compute.googleapis.com
gcloud services enable container.googleapis.com
gcloud services enable deploymentmanager.googleapis.com
gcloud services enable logging.googleapis.com
gcloud services enable monitoring.googleapis.com
gcloud services enable sqladmin.googleapis.com
```

7. Provide secrets in `deploy-public.yaml` file.
  * MySQL password (**<MySQL_PASSWORD>** placeholder) for Drupal and Wordpress -
  (`sql.properties.dbUser.password`, `drupal.properties.env.DB_PASSWORD`, `wp.properties.env.DB_PASSWORD`)
  * GCP ServiceAccount Base64 Key (*Base64_SA_Key.txt*) (**<B64_CRED_KEY>** placeholder) for CloudSQL Proxy Container -
  (`secret-service-account-drupal.properties.data` and `secret-service-account-wp.properties.data`)

8. Set pre-existing GCE Disk (**<GCE_DISK>** placeholder) for Drupal Nginx static files - `pv-nginx.properties.pdName`.
List available disks (**nginx-static-files** can be used for DEMO):
```
gcloud compute disks list
```
9. Set Service Account Email Address (**<KEY_EMAIL>** placeholder) - `sql.properties.cloudsql.serviceAccountEmailAddress`.

| Kubernetes and GCP Resources design  |   Kubernetes Network Policies design  |
| ------------ | ------------ |
|  ![Kubernetes and GCP Resources design](diagrams/kubernetes.png) | ![Kubernetes Network Policies design](diagrams/kubernetes_network_policy.png)  |

## Public Cluster

### Infrastructure Architecture

![Infrastructure Architecture Public](diagrams/public_gke.png)

### Deployment

#### Deploy Dev example

```
$ gcloud deployment-manager deployments create example-dev --config deploy-public.yaml
```

#### Deploy Test example

```
$ gcloud deployment-manager deployments create example-test --config deploy-public.yaml
```

#### Deploy Prod example

```
$ gcloud deployment-manager deployments create example-prod --config deploy-public.yaml
```

## Private Cluster

TBD

### Infrastructure Architecture

![Infrastructure Architecture Private](diagrams/private_gke.png)

### Deployment

TBD

## Authors

* **Piotr Kloskowski** - *Initial work* - [pklosk](https://github.com/pklosk)
