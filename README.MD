
# Get the argocli
https://argo-cd.readthedocs.io/en/stable/cli_installation/

# Login
ADMIN_PASSWD=$(oc get secret openshift-gitops-cluster -n openshift-gitops -o jsonpath='{.data.admin\.password}' | base64 -d)
SERVER_URL=$(oc get routes openshift-gitops-server -n openshift-gitops -o jsonpath='{.status.ingress[0].host}')
argocd login --username admin --password ${ADMIN_PASSWD} ${SERVER_URL}

# Basic Docs
https://docs.openshift.com/gitops/1.12/argocd_applications/creating-an-application-using-gitops-argocd-cli.html

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
  --server openshift-gitops-server-openshift-gitops.apps.wdharm41607261.migration.redhat.com  \
  --dest-server  https://kubernetes.default.svc 
