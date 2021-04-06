Currently to download from a private S3 bucket you create both a secret and a service account that you link it to

s3_secret.yaml
```
apiVersion: v1
kind: Secret
metadata:
  name: kfsecret
  annotations:
     serving.kubeflow.org/s3-endpoint: s3.amazonaws.com 
     serving.kubeflow.org/s3-usehttps: "1" # by default 1, for testing with minio you need to set to 0
     serving.kubeflow.org/s3-region: "us-east-2"
type: Opaque
stringData:
  AWS_ACCESS_KEY_ID: xxx
  AWS_SECRET_ACCESS_KEY: xxxx
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: servicesa
secrets:
  - name: kfsecret
```


```
$ kubectl apply -f s3_secret.yaml

secret/mysecret configured
serviceaccount/sa configured
```




You then set the serviceAccountName on the KFService 

pytorch_serve.yaml

```
apiVersion: "serving.kubeflow.org/v1alpha2"
kind: "InferenceService"
metadata:
  name: "s3-test-004"
spec:
  default:
    predictor:
      serviceAccountName: servicesa
      pytorch:
        storageUri: "s3://kfserving-samples/models/pytorch/cifar10/3"
        modelClassName: "Net"



```

```
$ kubectl apply -f pytorch_serve.yaml
inferenceservice.serving.kubeflow.org/s3-test-004 unchanged

$ kubectl get inferenceserviceNAME          URL                                      READY   DEFAULT TRAFFIC   CANARY TRAFFIC   AGE
s3-test-004   http://s3-test-004.default.example.com   True    100                                2m4s

```


Issue tracked here:
https://github.com/kubeflow/kfserving/issues/1519
