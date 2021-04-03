Currently to download from a private S3 bucket you create both a secret and a service account that you link it to

pytorch_serve.yaml

```
apiVersion: "serving.kubeflow.org/v1alpha2"
kind: "InferenceService"
metadata:
  name: "pytorch-s3-cifar10-005"
spec:
  default:
    predictor:
      serviceAccountName: sa
      tensorflow:
        storageUri: "s3://kfserving-samples/models/pytorch/cifar10"


```
