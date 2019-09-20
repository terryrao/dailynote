# istio_issue 

https://github.com/istio/istio/issues/5056

A container name must be specified for pod

When retrieving logs for pods that have multiple containers, you need to specify the container you want the logs for.

```bash
kubectl logs productpage-v1-84f77f8747-8zklx -c productpage
```



