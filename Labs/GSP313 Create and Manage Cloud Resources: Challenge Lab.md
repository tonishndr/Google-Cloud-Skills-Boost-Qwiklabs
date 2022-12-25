# [GSP313 Create and Manage Cloud Resources: Challenge Lab](https://www.cloudskillsboost.google/focuses/10258?parent=catalog)

<details><summary>Task 1. Create a project jumphost instance</summary>
</br>

- `gcloud config set compute/region [region]`
- `gcloud config set compute/zone [Zone]`
- `gcloud compute instances create [Instance name] \
--network=nucleus-vpc \
--zone=us-east1-b \
--machine-type=f1-micro \
--image-family=debian-10 \
--image-project=debian-cloud \
--scopes=cloud-platform \
--no-address`
</details>
  
<details><summary>Task 2. Create a Kubernetes service cluster</summary>
</br>

- `gcloud container clusters create [Cluster name]`
- `gcloud container clusters get-credentials [Cluster name]`
- `kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:2.0`
- `kubectl expose deployment hello-server --type=LoadBalancer --port [App port number]`
</details>

<details><summary>Task 3. Set up an HTTP load balancer</summary>

- `cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF`

- Create an instance template: `gcloud compute instance-templates create [Instance templates name] \
--metadata-from-file startup-script=startup.sh \
--network nucleus-vpc \
--machine-type g1-small`

- Create a target pool.

- Create a managed instance group: `gcloud compute instance-groups managed create [Instance group name] \
--template=[Instance templates name] \
--base-instance-name web-server \
--size 2 \
--template my-lb-template `

- Create a firewall rule named as Firewall rule to allow traffic (80/tcp): `gcloud compute firewall-rules create [Firewall rule] \
--allow tcp:80 \
--network nucleus-vpc`

- Create a health check: `gcloud compute health-checks create http [Health check name]`

- Create a backend service, and attach the managed instance group with named port (http:80): `gcloud compute instance-groups managed \
set-named-ports [Instance group name] \
--named-ports http:80`

- `gcloud compute backend-service create [Backend service name] \
--portocol=HTTP \
--port-name=http \
--health-checks=[Health check name] \
--global`

- `gcloud compute backend-service add-backend [Backend service name] \
--instance-group=[Instance group name] \
--global`

- Create a URL map, and target the HTTP proxy to route requests to your URL map: `gcloud compute url-maps create [Url maps name] \
--default-service=[Backend service name]`

- `gcloud compute target-http-proxy create [Target http proxy name]\
--url-map=[Url maps name]`

- Create a forwarding rule: `gcloud compute forwarding-rule create [Forwarding rules name] \
--target-http-proxy=[Target http proxy name] \
--ports=80 \
--global`
</details>
