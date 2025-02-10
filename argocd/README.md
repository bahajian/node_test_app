# ARGOCD



## Getting started

To make it easy for you to get started with ArgoCD, here's a list of recommended next steps.




#### Install ArgoCD CLI
------------------
```
brew install argocd
```
------------------
#### Install ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
------------------
#### Patch the Service
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer", "externalIPs": ["<YOUR_RESERVED_IP>"]}}'
```
------------------
#### Verify ArgoCD Service
```
kubectl get svc -n argocd
```
------------------
#### Retrieve ArgoCD Admin Password
```
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```
------------------
#### Login to ArgoCD
```
argocd login <YOUR_RESERVED_IP> --username admin --password <PASSWORD>
```
------------------


## ArgoCD Deployment



#### adding repository using ssh key
```
argocd repo add git@github.com:bahajian/node_test_app.git --ssh-private-key-path ~/.ssh/id_rsa
```

------------------
####
```
argocd cluster list
kubectl config get-contexts

argocd cluster add IBSTORM
argocd cluster list

argocd app create <MY_APP_NAME> \
  --repo git@github.com:your-username/your-repo.git \
  --path apps/guestbook \
  --dest-server <NEW_CLUSTER_SERVER_URL> \
  --dest-namespace <NAME_SPACE>


argocd app create node-test-app-red \
  --repo git@github.com:bahajian/node_test_app.git \
  --path k8s/using-ingress/red \
  --dest-server https://192.168.2.10:6443 \
  --dest-namespace node-test-app-red

argocd app sync node-test-app-red

argocd app set node-test-app-red --sync-policy automated

argocd app delete my-app

```
------------------


