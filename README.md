# azure_providerjet_crossplane

## Pre-Requisites

```bash
1. Kubernetes cluster
2. Install Crossplane
```

![image](https://user-images.githubusercontent.com/58024415/176353130-bb097a26-d423-44a6-8424-2c861e55ce3a.png)

## Install Crossplane with Helm

```bash
kubectl create namespace crossplane-system

helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
helm install crossplane --namespace crossplane-system crossplane-stable/crossplane
```

## Credentials File to communicate with Azure `crossplane-azure-provider-key.json`

```bash
{
  "clientId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "clientSecret": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "tenantId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

## Encrypting of Credentials

```bash
BASE64ENCODED_AZURE_ACCOUNT_CREDS=$(base64 crossplane-azure-provider-key.json | tr -d "\n")
```

## Copy Encrypted value in secret file

```bash
echo $BASE64ENCODED_AZURE_ACCOUNT_CREDS

---
apiVersion: v1
kind: Secret
metadata:
  name: azure-account-creds
  namespace:  crossplane-system
type: Opaque
data:
  credentials: <replace with BASE64ENCODED_AZURE_ACCOUNT_CREDS>
```

## Run secrets, azure provider and providerconfig

```bash
kubectl apply -f secret.yaml
kubectl apply -f provider.yaml
kubectl apply -f providerconfig.yaml
```

## Create services without xrd --> switch to direct folder

```bash
kubectl apply -f resource-group.yaml
kubectl apply -f network/
kubectl apply -f kubernetes/
```

## Create services with xrd, composition and claim --> switch to composition folder

```bash
kubectl apply -f xrd.yaml
kubectl apply -f composition.yaml
kubectl apply -f claim.yaml
```

## Check xrd, compositions and claims

```bash
kubectl get xrds
kubectl get compositions
kubectl get CompositeCluster
kubectl get managed
```
