# Create and Manage Cloud Resources: Challenge Lab

## Challenge scenario
You have started a new role as a Junior Cloud Engineer for Jooli, Inc. You are expected to help manage the infrastructure at Jooli. Common tasks include provisioning resources for projects.

You are expected to have the skills and knowledge for these tasks, so step-by-step guides are not provided.

Some Jooli, Inc. standards you should follow:

1. Create all resources in the default region or zone, unless otherwise directed.

2. Naming normally uses the format team-resource; for example, an instance could be named `nucleus-webserver1`.

3. Allocate cost-effective resource sizes. Projects are monitored, and excessive resource use will result in the containing project's termination (and possibly yours), so plan carefully. This is the guidance the monitoring team is willing to share: unless directed, use `f1-micro` for small Linux VMs, and use `n1-standard-1` for Windows or other applications, such as Kubernetes nodes.

## Your challenge
As soon as you sit down at your desk and open your new laptop, you receive several requests from the Nucleus team. Read through each description, and then create the resources.


### Task 1. Create a project jumphost instance
You will use this instance to perform maintenance for the project.

Requirements:

* Name the instance `Instance name`.
* Use an _f1-micro_ machine type.
* Use the default image type (Debian Linux).

### Task 2. Create a Kubernetes service cluster
> Note: There is a limit to the resources you are allowed to create in your project. If you don't get the result you expected, delete the cluster before you create another cluster. If you don't, the lab might end and you might be blocked. In order to get your account unblocked, you will have to reach out to Qwiklabs Support.

The team is building an application that will use a service running on Kubernetes. You need to:

* Create a cluster (in the us-east1-b zone) to host the service.
* Use the Docker container hello-app (`gcr.io/google-samples/hello-app:2.0`) as a place holder; the team will replace the container with their own work later.
* Expose the app on port `App port` number.

### Task 3. Set up an HTTP load balancer
You will serve the site via nginx web servers, but you want to ensure that the environment is fault-tolerant. Create an HTTP load balancer with a managed instance group of `2 nginx web servers`. Use the following code to configure the web servers; the team will replace this with their own configuration later.

> Note: There is a limit to the resources you are allowed to create in your project, so do not create more than 2 instances in your managed instance group. If you do, the lab might end and you might be banned.

```bash
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```
You need to:
* Create an instance template.
* Create a target pool.
* Create a managed instance group.
* Create a firewall rule named as Firewall rule to allow traffic (80/tcp).
* Create a health check.
* Create a backend service, and attach the managed instance group with named port (http:80).
* Create a URL map, and target the HTTP proxy to route requests to your URL map.
* Create a forwarding rule.

# Answer

## Task 1. Create a project jumphost instance
You will use this instance to perform maintenance for the project.

Requirements:

* Name the instance `Instance name`.
* Use an _f1-micro_ machine type.
* Use the default image type (Debian Linux).

__Answer__
```bash
# gcloud compute project-info describe --project <your_project_ID>
gcloud config get-value compute/zone
gcloud config get-value compute/region
# gcloud compute instances create --help
gcloud compute instances create jooli-instance-1 --machine-type f1-micro --image-family debian-11
```

## Task 2. Create a Kubernetes service cluster
> Note: There is a limit to the resources you are allowed to create in your project. If you don't get the result you expected, delete the cluster before you create another cluster. If you don't, the lab might end and you might be blocked. In order to get your account unblocked, you will have to reach out to Qwiklabs Support.

The team is building an application that will use a service running on Kubernetes. You need to:

* Create a cluster (in the us-east1-b zone) to host the service.
* Use the Docker container hello-app (`gcr.io/google-samples/hello-app:2.0`) as a place holder; the team will replace the container with their own work later.
* Expose the app on port `App port` number.

__Answer__
```bash
# set a default compute zone
gcloud config set compute/zone us-east1-b

# create a GKE cluster
export CLUSTER=jooli-us-east-cluster
gcloud container clusters create $CLUSTER

# get authentication credentials for cluster
gcloud container clusters get-credentials $CLUSTER 

## deploy an application to cluster
# create a deployment named hello-server from the hello-app container image
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:2.0

# create a Kubernetes service that expose application/container to external traffic and a Compute Engine load balancer for container
kubectl expose deployment hello-server --type=LoadBalancer --port 8080

# inspect hello-server Service. It may take a minute for an external IP to be generated if status is pending.
kubectl get service

# another way to check would be visiting external IP of hello-server
curl http://[EXTERNAL-IP]:8080


```

## Task 3. Set up an HTTP load balancer
You will serve the site via nginx web servers, but you want to ensure that the environment is fault-tolerant. Create an HTTP load balancer with a managed instance group of `2 nginx web servers`. Use the following code to configure the web servers; the team will replace this with their own configuration later.

> Note: There is a limit to the resources you are allowed to create in your project, so do not create more than 2 instances in your managed instance group. If you do, the lab might end and you might be banned.

```bash
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```

You need to:
* Create an instance template.
* Create a target pool.
* Create a managed instance group.
* Create a firewall rule named as Firewall rule to allow traffic (80/tcp).
* Create a health check.
* Create a backend service, and attach the managed instance group with named port (http:80).
* Create a URL map, and target the HTTP proxy to route requests to your URL map.
* Create a forwarding rule.

__Answer__
```bash
```