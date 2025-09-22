### Create Ubuntu root debug pod
---

``` bash
kubectl run debug-pod --rm -it --image=ubuntu --restart=Never --overrides='
{
  "spec": {
    "containers": [
      {
        "name": "debug-container",
        "image": "ubuntu",
        "stdin": true,
        "tty": true,
        "securityContext": {
          "runAsUser": 0,
          "runAsGroup": 0
        }
      }
    ]
  }
}' -- bash
```

**Explanation:**

- --image=ubuntu → full Ubuntu image with apt available.
- securityContext.runAsUser: 0 → ensures you run as root.
- stdin & tty → allows interactive shell.
- bash → starts bash shell.