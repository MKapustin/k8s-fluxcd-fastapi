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
```
docker build -t fastapi-fluxcd:0.0.0 .
docker run -d --name fastapi-fluxcd-conainer -p 8080:8080 fastapi-fluxcd:0.0.0
```


### To run a local linux playground
```docker run -it --rm -v ${HOME}:/root/ -v ${PWD}:/work -w /work --net host alpine sh```


### To run a local cluster and check it
```
kind create cluster --name fluxcd --image kindest/node:v1.30.4
kubectl cluster-info
```


### Flux bootstrap GitHub
```bash
flux bootstrap github \
  --token-auth \
  --owner=MKapustin \
  --repository=k8s-fluxcd-fastapi \
  --path=infra-repo/clusters/dev-cluster \
  --personal \
  --branch main

flux check

# flux manages itself using GitOps objects:
kubectl -n flux-system get GitRepository
kubectl -n flux-system get Kustomization
```


### GitOps structure
                                                    
```
                                                    
 developer    +-----------+     +----------+           
              |           |     | CI       |           
  ----------> | REPO(code)|---> | PIPELINE |           
              +-----------+     +----------+           
                                         |  commit     
                                         v             
           +----------+ sync    +----------+           
           |  INFRA   |-------> |INFRA     |           
           |  (k8s)   |         |REPO(yaml)|           
           +----------+         +----------+           
                                                            
```                                          


###
```bash
# go to our "git repo"
cd kubernetes/fluxcd/repositories/example-app-1
# check the files
ls

cd src
docker build . -t example-app-1:0.0.1

#load the image to our test cluster so we dont need to push to a registry
kind load docker-image example-app-1:0.0.1 --name fluxcd 
```


###
