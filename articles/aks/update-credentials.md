---
title: Réinitialiser les informations d’identification d’un cluster Azure Kubernetes Service (AKS)
description: Découvrir comment mettre à jour ou réinitialiser les informations d’identification de principal du service pour un cluster dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/30/2019
ms.author: iainfou
ms.openlocfilehash: 8552a22db1518fe41be78d67f91d74f0370abd93
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55479144"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Mettre à jour ou faire pivoter les informations d’identification d’un principal du service dans Azure Kubernetes Service (AKS)

Par défaut, les clusters AKS sont créés avec un principal de service dont le délai d’expiration est d’un an. À mesure que vous approchez de la date d’expiration, vous pouvez réinitialiser les informations d’identification afin de prolonger le délai d’expiration du principal de service. Vous souhaiterez peut-être également mettre à jour, ou faire pivoter, les informations d’identification dans le cadre d’une stratégie de sécurité définie. Cet article explique comment mettre à jour ces informations d’identification pour un cluster AKS.

## <a name="before-you-begin"></a>Avant de commencer

Azure CLI 2.0.56 (ou version ultérieure) doit être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Choisir de mettre à jour ou de créer un principal du service

Lorsque vous souhaitez mettre à jour les informations d’identification d’un cluster AKS, vous pouvez choisir de :

* mettre à jour les informations d’identification du principal du service existant utilisées par le cluster, ou
* créer un principal du service et mettre à jour le cluster pour qu’il utilise ces nouvelles informations d’identification.

Si vous souhaitez créer un principal du service puis mettre à jour le cluster AKS, ignorez les étapes restantes de cette section et passez à [créer un principal du service](#create-a-service-principal). Si vous souhaitez mettre à jour les informations d’identification du principal du service existant utilisées par le cluster AKS, suivez les étapes décrites dans cette section.

### <a name="get-the-service-principal-id"></a>Obtenir l’ID du principal du service

Pour mettre à jour les informations d’identification du principal du service existant, obtenez l’ID du principal du service de votre cluster à l’aide de la commande [az aks show][az-aks-show]. L’exemple suivant permet d’obtenir l’ID du cluster *myAKSCluster* dans le groupe de ressources *myResourceGroup*. L’ID du principal du service est défini en tant que variable pour être utilisé dans d’autres commandes.

```azurecli-interactive
SP_ID=$(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Mettre à jour les informations d’identification du principal du service

Avec un jeu de variables contenant l’ID du principal du service, réinitialisez les informations d’identification à l’aide de [az ad sp credential reset][az-ad-sp-credential-reset]. L’exemple suivant permet à la plateforme Azure de générer un nouveau secret sécurisé pour le principal du service. Ce nouveau secret sécurisé est également stocké dans une variable.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $AKS_SP --query password -o tsv)
```

Passez maintenant à [mettre à jour le cluster AKS avec les nouvelles informations d’identification](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Créer un principal du service

Si vous avez choisi de mettre à jour les informations d’identification du principal du service existantes dans la section précédente, ignorez cette étape. Passez à [mettre à jour le cluster AKS avec les nouvelles informations d’identification](#update-aks-cluster-with-new-credentials).

Pour créer un principal du service puis mettre à jour le cluster AKS pour qu’il utilise ces nouvelles informations d’identification, utilisez la commande [az ad sp create-for-rbac][az-ad-sp-create]. Dans l’exemple suivant, le paramètre `--skip-assignment` empêche toute affectation par défaut supplémentaire :

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Le résultat ressemble à l’exemple qui suit. Prenez note de vos propres valeurs pour `appId` et `password`. Ces valeurs sont utilisées à l’étape suivante.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Définissez maintenant des variables pour l’ID du principal du service et le secret client à l’aide du résultat de votre propre commande [az ad sp create-for-rbac][az-ad-sp-create], comme indiqué dans l’exemple suivant. *SP_ID* correspond à votre *appId*, et *SP_SECRET* à votre *mot de passe* :

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Mettre à jour le cluster AKS avec les nouvelles informations d’identification

Que vous ayez choisi de mettre à jour les informations d’identification du principal du service existant ou de créer un principal de service, vous mettez maintenant à jour le cluster AKS avec vos nouvelles informations d’identification à l’aide de la commande [az aks update-credentials][az-aks-update-credentials]. Les variables pour *--service-principal* et *--client-secret* sont utilisées :

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

La mise à jour des informations d’identification du principal du service dans AKS prend quelques instants.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, le principal du service du cluster AKS lui-même a été mis à jour. Pour plus d’informations sur la gestion de l’identité des charges de travail dans un cluster, consultez [Meilleures pratiques d’authentification et d’autorisation dans AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
