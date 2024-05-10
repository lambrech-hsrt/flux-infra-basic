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
