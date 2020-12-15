# Minikube Go Demo

In this project we will be deploying a simple Go application to a local kubernetes cluster using Minikube.

## Installation

First step is to install and start Minikube on your local machine, I have included installation instruction using the Minikube binary below. You can also use your favourite package manager such as [homebrew](https://brew.sh/) on mac, and [choco](https://chocolatey.org/) on windows, more information is available in the [Minikube documentation](https://minikube.sigs.k8s.io/docs/start/) .

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

You also need to have Docker installed locally, more information can be found [here](https://docs.docker.com/get-docker/).

## Run Minikube

Starting Minikube can be done with a simple command 

```bash
minikube start
...
# Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

You should get a confirmation that Minikube has started, and that kubectl is now configured to use Minikube cluster. If you don't have kubectl installed then Minikube can download the appropriate version of kubectl by running

```bash
minikube kubectl -- get po -A
```

## Deploying the demo app

Deploy the demo app to your Minikube cluster using

```bash
kubectl apply -f infra/k8s/demo-deployment.yml --validate=false
```

Note that this file contains both the deployment and the service. I have opted for a NodePort service as this is a simple example, for a production cluster a LoadBalancer service would be more appropriate.

To check on the status of the pods run:

```bash
kubctl get pods

# NAME                               READY   STATUS    RESTARTS   AGE
# demo-deployment-6674dcff77-99w7v   1/1     Running   0          40s
# demo-deployment-6674dcff77-qdw2v   1/1     Running   0          40s
# demo-deployment-6674dcff77-zx5cs   1/1     Running   0          40s
```

Check on the status of the service

```bash
kubectl get service

# NAME           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
# demo-service   NodePort    10.96.24.147   <none>        8080:31321/TCP   2m15s
# kubernetes     ClusterIP   10.96.0.1      <none>        443/TCP          38m
```

## Making a request

To get a URL that you can use to make a HTTP request to:

```bash
minikube service demo-service --url

# 🏃  Starting tunnel for service demo-service.
# |-----------|--------------|-------------|------------------------|
# | NAMESPACE |     NAME     | TARGET PORT |          URL           |
# |-----------|--------------|-------------|------------------------|
# | default   | demo-service |             | http://127.0.0.1:65224 |
# |-----------|--------------|-------------|------------------------|
```

You can now make a request to the cluster

```bash
curl http://127.0.0.1:65224

# Hello Equal Experts!!
```