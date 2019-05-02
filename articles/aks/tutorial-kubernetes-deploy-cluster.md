---
title: Didacticiel Kubernetes sur Azure – Déployer un cluster
description: Dans ce didacticiel Azure Kubernetes Service (AKS), vous créez un cluster AKS et utilisez kubectl pour vous connecter au nœud principal Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 302947ae9e952c0ceb37d9ddbba5ccd3c33c53a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031069"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Didacticiel : Déployer un cluster Azure Kubernetes Service (AKS)

Kubernetes fournit une plateforme distribuée destinée aux applications en conteneur. Avec AKS, vous pouvez créer rapidement un cluster Kubernetes prêt pour la production. Dans ce tutoriel (troisième d’une série de sept), un cluster Kubernetes est déployé dans AKS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un principal du service pour les interactions de la ressource
> * Déployer un cluster Kubernetes AKS
> * Installer l’interface de ligne de commande Kubernetes (kubectl)
> * Configurer kubectl pour se connecter à votre cluster AKS

Dans d’autres tutoriels, l’application Azure Vote est déployée sur le cluster, mise à l’échelle et mise à jour.

## <a name="before-you-begin"></a>Avant de commencer

Dans les tutoriels précédents, une image conteneur a été créée et chargée dans une instance Azure Container Registry. Si vous n’avez pas effectué ces étapes et que vous souhaitez suivre cette procédure, commencez par le [Tutoriel 1 : Créer des images conteneurs][aks-tutorial-prepare-app].

Ce tutoriel nécessite l’exécution de l’interface de ligne de commande Azure CLI version 2.0.53 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="create-a-service-principal"></a>Créer un principal du service

Pour permettre à un cluster AKS d’interagir avec d’autres ressources Azure, un principal du service Azure Active Directory est utilisé. Ce principal du service peut être créé automatiquement par Azure CLI ou le portail, ou vous pouvez en précréer un et lui affecter des autorisations supplémentaires. Dans ce tutoriel, vous créez un principal du service, vous accordez l’accès à l’instance Azure Container Registry (ACR) créé dans le tutoriel précédent, puis vous créez un cluster AKS.

Créez un principal du service à l’aide de la commande [az ad sp create-for-rbac][]. Le paramètre `--skip-assignment` limite l’affectation d’autorisations supplémentaires. Par défaut, ce principal de service est valide pendant un an.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Le résultat ressemble à l’exemple suivant :

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Prenez note des valeurs de *appId* et de *password*. Ces valeurs sont utilisées dans les étapes suivantes.

## <a name="configure-acr-authentication"></a>Configurer une authentification ACR

Pour accéder à des images stockées dans ACR, vous devez accorder au principal du service AKS les droits appropriés pour extraire des images d’ACR.

Pour commencer, obtenez l’ID de ressource ACR à l’aide de [az acr show][]. Mettez à jour le nom de Registre `<acrName>` avec celui de votre instance ACR et le groupe de ressources où se trouve cette instance.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Pour accorder l’accès qui permettra au cluster AKS de tirer (pull) des images stockées dans ACR, attribuez le rôle `AcrPull` à l’aide de la commande [az role assignment create][]. Remplacez `<appId`> et `<acrId>` par les valeurs recueillies au cours des deux étapes précédentes.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Créer un cluster Kubernetes

Les clusters AKS peuvent utiliser les contrôles d’accès en fonction des rôles Kubernetes (RBAC). Ces contrôles vous permettent de définir l’accès aux ressources en fonction des rôles attribués aux utilisateurs. Des autorisations sont combinées si plusieurs rôles sont attribués à un utilisateur, et les autorisations peuvent être limitées à un seul espace de noms ou accordées à l’ensemble du cluster. Par défaut, l’interface de ligne de commande Azure active automatiquement RBAC lorsque vous créez un cluster AKS.

Créez un cluster AKS à l’aide de [az aks create][]. L’exemple suivant crée un cluster nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*. Vous avez créé ce groupe de ressources dans le [tutoriel précédent][aks-tutorial-prepare-acr]. Spécifiez vos propres valeurs pour `<appId>` et `<password>`, qui proviennent de l’étape précédente le principal du service a été créé.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Après quelques minutes, le déploiement se termine et retourne des informations au format JSON concernant le déploiement AKS.

## <a name="install-the-kubernetes-cli"></a>Installer l’interface de ligne de commande Kubernetes

Pour vous connecter au cluster Kubernetes à partir de votre ordinateur local, vous utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes.

Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. Vous pouvez également l’installer en local à l’aide de la commande [az aks install-cli][] :

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Se connecter au cluster à l’aide de kubectl

Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials][]. L’exemple suivant obtient les informations d’identification du cluster AKS nommé *myAKSCluster* dans le groupe de ressources *myResourceGroup* :

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pour vérifier la connexion à votre cluster, exécutez la commande [kubectl get nodes][kubectl-get] :

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, un cluster Kubernetes a été déployé dans ACS, et vous avez configuré `kubectl` pour qu’il s’y connecte. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un principal du service pour les interactions de la ressource
> * Déployer un cluster Kubernetes AKS
> * Installer l’interface de ligne de commande Kubernetes (kubectl)
> * Configurer kubectl pour se connecter à votre cluster AKS

Passez au didacticiel suivant pour savoir comment déployer une application dans le cluster.

> [!div class="nextstepaction"]
> [Déployer une application dans Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
