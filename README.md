### Kubeflow-Serving

Here we are going to install kfserving on a 12 GB RAM + 4 core machine
And test its execution

What you need:
```
Ubuntu 18.04 (In BIOS enable Nested VT-x/AMD-V)
Virtualbox
kubectl
Docker
minikube
```
Once you have installed above, set :
```
minikube config set memory 12288
minikube config set cpus 4
```
Now start minikube:

```
minikube starthttps://github.com/kubeflow/kfserving.git
```

Clone kfserving:
```
git clone https://github.com/kubeflow/kfserving.git

cd kfserving
./hack/quick_install.sh
```

