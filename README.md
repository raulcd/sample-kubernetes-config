# Sample Kubernetes Config

This repo is an example of the default config I set up for a kubernetes cluster.

In the default folder:

- ingress-controller
- external-dns
- cert-manager

## Ingress controller

Apply ingress controller and default nginx:

```
kubectl apply -f ingress-controller-base.yml
kubectl apply -f gcp-nginx.yml
```

## External DNS

For external DNS to work I've had to change the default scopes for google nodes to have permissions to read/write on DNS.

```
gcloud container node-pools create adjust-scope --cluster $YOUR_CLUSTER_NAME --zone $YOUR_ZONE --num-nodes 1 --scopes "default,https://www.googleapis.com/auth/ndev.clouddns.readwrite,https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append"
```

In order to remove the default node pool cordon and drain the node.

In order to manage the dns zone:

```
gcloud dns managed-zones create "myzone" --dns-name "dev.com." --description "Automatically managed zone by kubernetes.io/external-dns for dev.com"
```

Add the following NS to your DNS:
```
$ gcloud dns record-sets list --zone "myzone" --name "dev.com." --type NS
NAME          TYPE  TTL    DATA
dev.com.  NS    21600  ns-cloud-a1.googledomains.com.,ns-cloud-a2.googledomains.com.,ns-cloud-a3.googledomains.com.,ns-cloud-a4.googledomains.com.
```

Finally apply external-dns (modify the file with your own domain, email and project if google cloud):

```
kubectl apply -f external-dns.yml
```

## Cert manager:

Apply cert manager, modify ClusterIssuer with your email:

```
kubectl apply -f cert-manager.yml
```

# Web sample is just an example config for a website

Modify domain names and image to run.

```
kubectl apply -f web-namespace.yml
kubectl apply -f web-configmap.yml
kubectl apply -f web-deployment.yml
kubectl apply -f web-service.yml
kubectl apply -f web-ingress.yml
```