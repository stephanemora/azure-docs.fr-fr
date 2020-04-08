---
title: Utiliser les identités managées dans Azure Kubernetes Service
description: Découvrez comment utiliser les identités managées dans Azure Kubernetes Service (AKS).
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369969"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Utiliser les identités managées dans Azure Kubernetes Service

Actuellement, un cluster Azure Kubernetes Service ou AKS (plus précisément, le fournisseur cloud Kubernetes) nécessite un *principal de service* pour créer des ressources supplémentaires telles que des équilibreurs de charge et des disques managés dans Azure. Soit vous fournissez un principal de service, soit AKS en crée un en votre nom. Les principaux de service ont généralement une date d’expiration. Les clusters finissent par atteindre un état dans lequel le principal de service doit être renouvelé pour que le cluster continue de fonctionner. Gérer les principaux de service n'est pas une mince affaire.

Les *identités managées* correspondent essentiellement à un wrapper autour des principaux de service, ce qui simplifie leur gestion. Pour en savoir plus, découvrez les [identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS crée deux identités managées :

- **Identité managée affectée par le système** : identité utilisée par le fournisseur de cloud Kubernetes pour créer des ressources Azure au nom de l’utilisateur. Le cycle de vie de l’identité affectée par le système est lié à celui du cluster. L’identité est supprimée en même temps que le cluster.
- **Identité managée affectée par l’utilisateur** : identité utilisée pour l’autorisation dans le cluster. Par exemple, l’identité affectée par l’utilisateur permet d’autoriser AKS à utiliser des registres Azure Container Registry et d’autoriser le kubelet à obtenir des métadonnées d’Azure.

Les modules complémentaires s’authentifient également à l’aide d’une identité managée. Pour chaque module complémentaire, une identité managée est créée par AKS et la durée de vie de celle-ci sera la même que celle du module complémentaire. Pour créer et utiliser votre propre réseau virtuel, une adresse IP statique ou un disque Azure attaché où les ressources se trouvent en dehors du groupe de ressources MC_*, utilisez le PrincipalID du cluster pour effectuer une attribution de rôle. Pour plus d’informations sur l’attribution de rôle, consultez [Déléguer l’accès à d’autres ressources Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

## <a name="before-you-begin"></a>Avant de commencer

La ressource suivante doit être installée :

- Azure CLI, version 2.2.0 ou ultérieure

## <a name="create-an-aks-cluster-with-managed-identities"></a>Créer un cluster AKS avec des identités managées

Vous pouvez désormais créer un cluster AKS avec des identités managées à l’aide des commandes CLI suivantes.

Commencez par créer un groupe de ressources Azure :

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Créez ensuite un cluster AKS :

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Un cluster créé à l’aide d’identités managées contient les informations de profil du principal de service suivantes :

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Obtenez enfin les informations d’identification pour accéder au cluster :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

La création du cluster prend quelques minutes. Vous pouvez ensuite déployer vos charges de travail d’application sur le nouveau cluster et interagir avec celui-ci comme vous le faisiez avec les clusters AKS basés sur le principal de service.

> [!IMPORTANT]
>
> - Les clusters AKS avec des identités managées ne peuvent être activés que pendant la création du cluster.
> - Il est impossible de mettre à jour ou de mettre à niveau des clusters AKS existants pour activer des identités managées.
