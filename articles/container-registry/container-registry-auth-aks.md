---
title: S’authentifier auprès d’Azure Container Registry à partir d’Azure Kubernetes Service
description: Découvrez comment fournir un accès aux images de votre registre de conteneurs privé à partir d’Azure Kubernetes Service à l’aide d’un principal de service Azure Active Directory.
services: container-service
author: dlepow
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.openlocfilehash: 0dbdf2261b851b303a0c606e5de70354578c6d2e
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078777"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>S’authentifier auprès d’Azure Container Registry à partir d’Azure Kubernetes Service

Quand vous utilisez Azure Container Registry (ACR) avec Azure Kubernetes Service (AKS), vous avez besoin d’un mécanisme d’authentification. Cet article décrit en détail les configurations recommandées pour l’authentification entre ces deux services Azure.

## <a name="grant-aks-access-to-acr"></a>Accorder à AKS un accès à ACR

Lorsque vous créez un cluster AKS, Azure crée également un principal du service pour prendre en charge le bon fonctionnement du cluster avec d’autres ressources Azure. Vous pouvez aussi utiliser ce principal du service généré automatiquement pour l’authentification sur un registre ACR. Pour ce faire, vous devez créer une [attribution de rôle](../role-based-access-control/overview.md#role-assignments) Azure AD qui accorde au principal du service du cluster l’accès au registre de conteneurs.

Utilisez le script suivant pour accorder au principal du service généré par AKS l’accès par extraction à un registre de conteneurs Azure. Modifiez les variables `AKS_*` et `ACR_*` de votre environnement avant d’exécuter le script.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Accès à l’aide d’une clé secrète Kubernetes

Dans certains cas, vous ne pourrez peut-être pas assigner le rôle requis au principal du service AKS généré automatiquement lui accordant l’accès à ACR. Par exemple, en raison du modèle de sécurité de votre organisation, vous ne disposez peut-être pas d’autorisations suffisantes dans votre Azure AD pour assigner un rôle au principal du service généré par AKS. L’assignation d’un rôle à un principal du service nécessite que votre compte Azure AD dispose d’une autorisation en écriture sur votre locataire Azure AD. Si vous n’avez pas d’autorisation, vous pouvez créer un principal du service, puis lui accorder l’accès au registre de conteneurs à l’aide d’un secret de tirage (pull) d’image Kubernetes.

Utilisez le script suivant pour créer un nouveau principal du service (vous allez utiliser ses informations d’identification pour le secret de tirage (pull) d’image Kubernetes). Modifiez la variable `ACR_NAME` de votre environnement avant d’exécuter le script.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Vous pouvez désormais stocker les informations d’identification du principal de service dans un [secret de tirage (pull) d’images][image-pull-secret] Kubernetes, référencé par votre cluster AKS lors de l’exécution des conteneurs.

Utilisez la commande suivante **kubectl** pour créer le secret Kubernetes. Remplacez `<acr-login-server>` par le nom qualifié complet de votre registre de conteneurs Azure (au format « acrname.azurecr.io »). Remplacez `<service-principal-ID>` et `<service-principal-password>` par les valeurs obtenues lors de l’exécution du script précédent. Remplacez `<email-address>` par n’importe quelle adresse e-mail bien formée.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Vous pouvez maintenant utiliser le secret Kubernetes dans les déploiements de pod en spécifiant son nom (dans ce cas, « acr-auth ») dans le paramètre `imagePullSecrets` :

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
