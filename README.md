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
minikube start
```

Clone and install kfserving:
```
git clone https://github.com/kubeflow/kfserving.git

cd kfserving
./hack/quick_install.sh
```

sometimes this install fails . Retry installing... sometimes dependencies are not locked in properly from the web.

As we are deploying on laptop we follow below steps (if you are deploying on cloud refer here: https://github.com/kubeflow/kfserving#determine-the-ingress-ip-and-ports   )


```
cd kfserving/docs/samples/v1alpha2/pytorch
kubectl apply -f pytorch.yaml
```

After applying yaml I see this:

```
$ kubectl get inferenceservice

NAME              URL                                          READY   PREV   LATEST   PREVROLLEDOUTREVISION   LATESTREADYREVISION                       AGE
pytorch-cifar10   http://pytorch-cifar10.default.example.com   True           100                              pytorch-cifar10-predictor-default-ssvd9   4m58s
```

```
INGRESS_GATEWAY_SERVICE=$(kubectl get svc --namespace istio-system --selector="app=istio-ingressgateway" --output jsonpath='{.items[0].metadata.name}')
kubectl port-forward --namespace istio-system svc/${INGRESS_GATEWAY_SERVICE} 8080:80
# start another terminal
export INGRESS_HOST=localhost
export INGRESS_PORT=8080
```

Now install jsonquery for preety formatting of response:
```
sudo apt  install jq
```

Now lets send curl request :

```
MODEL_NAME=pytorch-cifar10
INPUT_PATH=@./input.json
SERVICE_HOSTNAME=$(kubectl get inferenceservice pytorch-cifar10 -o jsonpath='{.status.url}' | cut -d "/" -f 3)

curl -v -H "Host: ${SERVICE_HOSTNAME}" -d $INPUT_PATH http://${INGRESS_HOST}:${INGRESS_PORT}/v1/models/$MODEL_NAME:predict | jq
```

Response:

```
$ curl -v -H "Host: ${SERVICE_HOSTNAME}" -d $INPUT_PATH http://${INGRESS_HOST}:${INGRESS_PORT}/v1/models/$MODEL_NAME:predict | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8080 (#0)
> POST /v1/models/pytorch-cifar10:predict HTTP/1.1
> Host: pytorch-cifar10.default.example.com
> User-Agent: curl/7.58.0
> Accept: */*
> Content-Length: 110681
> Content-Type: application/x-www-form-urlencoded
> Expect: 100-continue
> 
< HTTP/1.1 100 Continue
} [16384 bytes data]
* We are completely uploaded and fine
< HTTP/1.1 200 OK
< content-length: 225
< content-type: application/json; charset=UTF-8
< date: Wed, 24 Mar 2021 09:11:14 GMT
< server: istio-envoy
< x-envoy-upstream-service-time: 7
< 
{ [225 bytes data]
100  108k  100   225  100  108k   2647  1271k --:--:-- --:--:-- --:--:-- 1274k
* Connection #0 to host localhost left intact
{
  "predictions": [
    [
      -1.6099599599838257,
      -2.6461074352264404,
      0.3284447193145752,
      2.4825074672698975,
      0.4352458715438843,
      2.3108041286468506,
      1.0005676746368408,
      -0.42327648401260376,
      -0.5100946426391602,
      -1.7978392839431763
    ]
  ]
}

```








