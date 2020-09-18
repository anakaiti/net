---
title: 'Deleting kubernetes namespaces stuck on terminating'
date: 2020-09-18T10:56:42+07:00
draft: false
---

## Why are namespaces stuck on deleting?

Kubernetes resources have a concept called finalizers, which is a hook that is called before a resource is deleted. The resource will only be deleted if all the hook succeeds.  
Sometimes, your cluster setup has custom finalizers, usually when using CRDs. However, when deleting namespaces, it's possible that the finalizer service is deleted first before the resources with that finalizer is deleted, making the hook fail.  
To properly delete namespaces with custom finalizers, you should delete the resources with the custom finalizer first, before deleting the finalizer service. Mistakes happen though, and that's why you're probably reading this.


**Key: Remove all resource finalizers!**


## 1. Locate the remaining resources

Here's the command that will list all remaining resources in the namespace
```sh
kubectl api-resources --verbs=list --namespaced -o name \
    | xargs -n 1 kubectl get --show-kind --ignore-not-found  -n {YOUR-NAMESPACE}
```
The output should be something like
```
applications/argocd
applications/dev-ingress
```

## 2. Delete the finalizer in the resources

Now that you know the resources, edit the resource manifest one by one and delete the finalizer block.

```sh
# Edit the resources
kubectl edit applications/argocd
```

```diff
  [...]
  spec:
+    finalizers: []
-    finalizers:
-    - kubernetes
```

After editing the resources, it should be instantly gone. Repeat this until all the resources are gone, and your namespace should be gone too!

## 3. If it's still stuck

If the namespace is somehow still stuck. You need to delete the finalizer in the `namespace` object. The steps should be the same as in #2.

```sh
# Edit the namespace
kubectl edit namespace/argocd

```

## It's finally gone!

Your namespace should be gone by now. Please note that __This is a hack!__. Not properly finalizing resources may result in unexpected behavior, and you should always clean up your resources properly before deleting a namespace.

## Further reading

- [Extend the Kubernetes API with CustomResourceDefinitions | Kubernetes](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#finalizers)
