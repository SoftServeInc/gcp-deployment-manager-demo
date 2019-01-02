# GKE Deployment Manager Demo

Demo project to demonstrate GCP Deployment Manager automation tool capabilities for building GKE clusters and deploying example applications.

## Getting Started

These instructions will get you a fully functional GKE Regional Cluster including deployed example applications. See deployment for notes on how to deploy the cluster.

### Prerequisites

Google Cloud Platform Project.  
GCloud Command Line Tool.

## Secrets and additional settings

1. Create image with nginx static files from Google Cloud Storage:
```
gcloud compute images create gcp-dm-demo-drupal-nginx-static-files --source-uri https://storage.googleapis.com/gcp-dm-demo/gcp-dm-demo-drupal-nginx-static-files.tar.gz --project <PROJECT-ID>
```
2. Create Compute Disk from image:
```
gcloud compute disks create nginx-static-files --image gcp-dm-demo-drupal-nginx-static-files --zone europe-west3-b
```
3. Create a service account:
```
gcloud iam service-accounts create gcp-dm-demo
```
and follow the instruction to download service account JSON key: https://cloud.google.com/iam/docs/creating-managing-service-account-keys.

4. Run the following command to get base64 encoded service account key:
```
base64 -w 0 <service_account_JSON_key_file>
```
5. Assign CloudSQL Admin Role to service account
6. Enable the following APIs for your project:

* Compute Engine API
* Kubernetes Engine API
* Google Cloud Deployment Manager V2 API
* Stackdriver Logging API
* Stackdriver Monitoring API
* Cloud SQL Admin API

7. Provide secrets in `deploy-public.yaml` file.

* MySQL password for Drupal and Wordpress (`sql.properties.dbUser.password`, `drupal.properties.env.DB_PASSWORD`, `wp.properties.env.DB_PASSWORD`)
* GCP Service Account JSON Credentials (SQL Admin role assigned, base64 encoded) for CloudSQL Proxy Container - (`secret-service-account-drupal.properties.data` and `secret-service-account-wp.properties.data`)

8. Set pre-existing GKE Persistent Disk Name for Drupal Nginx static files - `pv-nginx.properties.pdName`.
9. Set Service Account Email Address - `sql.properties.cloudsql.serviceAccountEmailAddress`.

## Kubernetes and GCP Resources design

![Kubernetes and GCP Resources design](diagrams/kubernetes.png)

## Kubernetes Network Policies design

![Kubernetes Network Policies design](diagrams/kubernetes_network_policy.png)

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
