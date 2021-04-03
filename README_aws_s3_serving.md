Currently to download from a private S3 bucket you create both a secret and a service account that you link it to

pytorch_serve.yaml

```
apiVersion: "serving.kubeflow.org/v1alpha2"
kind: "InferenceService"
metadata:
  name: "mnist-s3"
spec:
  default:
    predictor:
      serviceAccountName: sa
      tensorflow:
        storageUri: "s3://mnist/v1/export"

```
