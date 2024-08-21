### To install local K8S tooling
```bash
#!/bin/bash

# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.24.0/kind-linux-amd64
chmod +x ./kind
sudo cp ./kind /usr/local/bin/kind
rm -rf kind
```

```bash

[ $(uname -m) = x86_64 ] && curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo cp ./kubectl /usr/local/bin/kubectl
rm -rf kubectl
```


```bash
curl -o /tmp/flux.tar.gz -sLO https://github.com/fluxcd/flux2/releases/download/v2.3.0/flux_2.3.0_linux_amd64.tar.gz
tar -C /tmp/ -zxvf /tmp/flux.tar.gz
mv /tmp/flux /usr/local/bin/flux
chmod +x /usr/local/bin/flux

flux check --pre
```

### To build image and run App in container
`docker build -t fastapi-fluxcd:0.0.0 .`
`docker run -d --name fastapi-fluxcd-conainer -p 80:80 fastapi-fluxcd:0.0.0`


### To run a local linux playground
docker run -it --rm -v ${HOME}:/root/ -v ${PWD}:/work -w /work --net host alpine sh


### To run a local cluster and check it
kind create cluster --name fluxcd --image kindest/node:v1.30.4
kubectl cluster-info



### Flux bootstrap GitHub
flux bootstrap github \
  --token-auth \
  --owner=MKapustin \
  --repository=fluxcd-k8s-fastapi \
  --path=infra-repo/clusters/dev-cluster \
  --personal \
  --branch main

flux check

# flux manages itself using GitOps objects:
kubectl -n flux-system get GitRepository
kubectl -n flux-system get Kustomization