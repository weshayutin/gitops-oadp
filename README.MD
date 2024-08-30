
# Get the argocli
https://argo-cd.readthedocs.io/en/stable/cli_installation/

# Login
ADMIN_PASSWD=$(oc get secret openshift-gitops-cluster -n openshift-gitops -o jsonpath='{.data.admin\.password}' | base64 -d)
SERVER_URL=$(oc get routes openshift-gitops-server -n openshift-gitops -o jsonpath='{.status.ingress[0].host}')
argocd login --username admin --password ${ADMIN_PASSWD} ${SERVER_URL}


argocd app create minimal-8csivol \
  --repo https://github.com/weshayutin/gitops-oadp.git \
  --path . \
  --dest-namespace minimal-8csivol \
  --auto-prune \
  --self-heal \
  --sync-policy automated \
  --sync-option CreateNamespace=true \
  --grpc-web
