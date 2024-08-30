# GitOps (Argo) and OADP (Velero)

### Install the OpenShift gitops operator
ah, you know how :)

### Get the argocli
https://argo-cd.readthedocs.io/en/stable/cli_installation/

### Login
```
ADMIN_PASSWD=$(oc get secret openshift-gitops-cluster -n openshift-gitops -o jsonpath='{.data.admin\.password}' | base64 -d)
```

```
SERVER_URL=$(oc get routes openshift-gitops-server -n openshift-gitops -o jsonpath='{.status.ingress[0].host}')
```
```
argocd login --username admin --password ${ADMIN_PASSWD} ${SERVER_URL}
```

### Get the argocd route or context
```
ARGO_SERVER=`oc get route -n openshift-gitops -o jsonpath='{range .items[1]}{.spec.host}'`
```
* check this by running
```
argo context
```

### Basic Docs
https://docs.openshift.com/gitops/1.12/argocd_applications/creating-an-application-using-gitops-argocd-cli.html

### Sample invocation 
```
argocd app create minimal-8csivol \
  --repo https://github.com/weshayutin/gitops-oadp.git \
  --path app \
  --revision main \
  --dest-namespace minimal-8csivol \
  --directory-recurse \
  --auto-prune \
  --self-heal \
  --sync-policy automated \
  --sync-option CreateNamespace=true \
  --grpc-web \
  --server $ARGO_SERVER  \
  --dest-server  https://kubernetes.default.svc 
```

### Then you have to label the namespace for things to kick off
```
oc label ns minimal-8csivol "argocd.argoproj.io/managed-by=openshift-gitops"
```


## Known Errors with misconfigured Argo 

Argo and OADP need to be configured properly when they are used together in the same cluster and namespace.  Both Argo and OADP assume to have full control over creating and deleting objects and resources in the namespace.

* A CSI DataMover backup will fail with volumesnapshots not found

  * Backup
```
velero backup create 8csi-argo1 --include-namespaces minimal-8csivol --snapshot-move-data=true
```
  * Failure - [volumesnapshots not found](known_errors/volumesnapshot_not_found.txt)
