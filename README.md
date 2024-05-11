# 🤖 flux-infra-basic
Basic Flux Infrastructure Repo (single instance / single cluster) with a few demo applications (helm, yaml, kustomize))

**This Demo is using one instance for one cluster**

# Folder Structure

```
flux-infra-basic
└── clusters
    ├── default
    │   └── guestbook-ui
    │       └── ...
    ├── ...
    └── my-cluster
        └── flux-system
            ├── gotk-components.yaml
            ├── gotk-sync.yaml
            └── kustomization.yaml
```

## Bootstrapping this Demo

```sh
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>
export GIT_REPO=<REPO_URL>

flux bootstrap github \
  --components-extra=image-reflector-controller,image-automation-controller \
  --owner=$GITHUB_USER \
  --repository=$GIT_REPO \
  --branch=main \
  --path=./clusters/my-cluster \
  --personal
```

## Sync

You can view the Reconcilation Process for Kustomization with following command:
```sh
flux get kustomizations --watch
```

## GitOps Weave UI (Dashboard)

Guides to install the Weave Dashboard are rare. here I will show the method that worked for me.
In this Repo the UI is pre configured if you fork this Repo you don't have to do the following steps again.

default **username** and **password** in this Repo:
```
username: admin
password: password
```

If you want to install the UI in your own Repo you can follow this Guide:

### GitOps CLI
First you need to install the Weave GitOps CLI Tool, this tool makes it more easy for us the create the yaml
manifest Files automated:

```sh
brew tap weaveworks/tap
brew install weaveworks/tap/gitops
gitops version
```

or more generic way to install:
```sh
curl --silent --location "https://github.com/weaveworks/weave-gitops/releases/download/v0.30.0/gitops-$(uname)-$(uname -m).tar.gz" | tar xz -C /tmp
sudo mv /tmp/gitops /usr/local/bin
```

### Generate weave-gitops configuration
```sh
gitops create dashboard ww-gitops \
  --password=password \
  --export > ./clusters/my-cluster/weave/weave-gitops-dashboard.yaml
```

This command generates 2 components for flux:
- ``HelmRepository`` describing where the helm chart is hosted
- ``HelmRelease`` describes how to deploy the chart into the cluster

now you can commit and push the changes 
```sh
git add .
git commit -am"added weave UI configuration"
```

After Reconcilation you should see the new pod creating in the ``flux-system`` namespace:
```sh
NAME                                           READY   STATUS              RESTARTS   AGE
kustomize-controller-7b7b47f459-c6fvb          1/1     Running             0          29m
source-controller-7667765cd7-f2nzx             1/1     Running             0          29m
notification-controller-5bb6647999-49zz7       1/1     Running             0          29m
helm-controller-5d8d5fc6fd-kdw78               1/1     Running             0          29m
image-reflector-controller-68648dcb68-92r7f    1/1     Running             0          28m
image-automation-controller-659765677d-9sg2b   1/1     Running             0          28m
ww-gitops-weave-gitops-6fc66d8597-rz98r        0/1     ContainerCreating   0          40s
```

Wait until the pod ist finished. After That you can expose the UI with the following command:
```sh
kubectl port-forward svc/ww-gitops-weave-gitops -n flux-system 9001:9001
```

now you can open 127.0.0.1:9001

### UI

Login Window

<img src="https://github.com/lambrech-hsrt/flux-infra-basic/blob/main/.img/login.png" alt="login screen">

Application Overview:

<img src="https://github.com/lambrech-hsrt/flux-infra-basic/blob/main/.img/applications.png" alt="app overview">

