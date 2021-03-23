# Kubeflow-Serving

```
apiVersion: "serving.kubeflow.org/v1alpha2"
kind: "InferenceService"
metadata:  
  name: "my-tf-flowers-model"
spec:  
  default:     
    predictor:      
      tensorflow:        
        storageUri: "gs://kfserving-samples/models/tensorflow/flowers-2"
```
