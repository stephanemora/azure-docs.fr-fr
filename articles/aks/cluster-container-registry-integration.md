---
title: Intégrer Azure Container Registry avec Azure Kubernetes Service
description: Découvrez comment intégrer Azure Kubernetes Service (AKS) à Azure Container Registry (ACR)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: ab744efd205d826cb7ae2c3eda7bba28f4a9bee0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097802"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>S’authentifier auprès d’Azure Container Registry à partir d’Azure Kubernetes Service

Quand vous utilisez Azure Container Registry (ACR) avec Azure Kubernetes Service (AKS), vous avez besoin d’un mécanisme d’authentification. Cet article décrit en détail les configurations recommandées pour l’authentification entre ces deux services Azure.

Vous pouvez configurer l’intégration de AKS à ACR à l’aide de quelques commandes simples avec Azure CLI.

## <a name="before-you-begin"></a>Avant de commencer

Vous devez disposer des éléments suivants :

* Le rôle **Propriétaire** ou **Administrateur de compte Azure** sur **l’abonnement Azure**
* Vous devez aussi disposer d’Azure CLI version 2.0.73 ou ultérieure
* Vous avez besoin d’un [Docker installé](https://docs.docker.com/install/) sur votre client, et vous devez avoir accès au [Hub Docker](https://hub.docker.com/)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Créer un nouveau cluster AKS avec l’intégration ACR

Vous pouvez configurer l’intégration d’AKS et d’ACR lors de la création initiale de votre cluster AKS.  Pour permettre à un cluster AKS d’interagir avec ACR, un **principal du service** Azure Active Directory est utilisé. La commande CLI suivante vous permet d’autoriser un ACR existant dans votre abonnement et de configurer le rôle **ACRPull** approprié pour le principal du service. Fournissez des valeurs valides pour vos paramètres ci-dessous.  Les paramètres entre crochets sont facultatifs.
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
**Un ID de ressource ACR possède le format suivant :** 

/subscriptions/<ID-abonnement>/resourceGroups/<nom-groupe-de-ressources>/providers/Microsoft.ContainerRegistry/registries/{nom} 
  
Cette étape peut prendre plusieurs minutes.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurer une intégration ACR pour les clusters AKS existants

Intégrez un ACR existant à des clusters AKS existants en fournissant des valeurs valides pour **acr-name** ou **acr-resource-id** comme indiqué ci-dessous.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Vous pouvez également supprimer l’intégration entre ACR et un cluster AKS à l’aide de ce qui suit
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>Connexion à votre ACR

Exécutez la commande suivante pour vous connecter à votre ACR.  Remplacez le paramètre <acrname> par le nom de votre ACR.  Par exemple, la valeur par défaut est **aks<votre-groupe-de-ressources>acr**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Extraire une image à partir du Hub Docker et la transmettre à votre ACR

Extrayez une image du Hub Docker, étiquetez l’image, puis envoyez-la vers votre ACR.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Mettre à jour l’état et vérifier les pods

Pour vérifier votre déploiement, procédez comme suit.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Affichez le fichier YAML et modifiez la propriété image en remplaçant la valeur par votre serveur de connexion ACR, votre image et votre étiquette.

```
$ cat acr-nginx.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
