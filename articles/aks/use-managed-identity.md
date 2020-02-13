---
title: Utiliser les identités managées dans Azure Kubernetes Service
description: Découvrez comment utiliser les identités managées dans Azure Kubernetes Service (AKS).
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 10ec07cab94184810e083c643e862cebe8e2431c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121435"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Préversion - Utiliser les identités managées dans Azure Kubernetes Service

Actuellement, un cluster Azure Kubernetes Service ou AKS (plus précisément, le fournisseur cloud Kubernetes) nécessite un *principal de service* pour créer des ressources supplémentaires telles que des équilibreurs de charge et des disques managés dans Azure. Soit vous fournissez un principal de service, soit AKS en crée un en votre nom. Les principaux de service ont généralement une date d’expiration. Les clusters finissent par atteindre un état dans lequel le principal de service doit être renouvelé pour que le cluster continue de fonctionner. Gérer les principaux de service n'est pas une mince affaire.

Les *identités managées* correspondent essentiellement à un wrapper autour des principaux de service, ce qui simplifie leur gestion. Pour en savoir plus, découvrez les [identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS crée deux identités managées :

- **Identité managée affectée par le système** : identité utilisée par le fournisseur de cloud Kubernetes pour créer des ressources Azure au nom de l’utilisateur. Le cycle de vie de l’identité affectée par le système est lié à celui du cluster. L’identité est supprimée en même temps que le cluster.
- **Identité managée affectée par l’utilisateur** : identité utilisée pour l’autorisation dans le cluster. Par exemple, l’identité affectée par l’utilisateur sert à autoriser AKS à utiliser des enregistrements de contrôle d’accès (ACR) ou à autoriser le kubelet à obtenir des métadonnées d’Azure.

Dans cette préversion, un principal de service reste nécessaire. Il est utilisé à des fins d’autorisation de modules complémentaires tels que la supervision, les nœuds virtuels, Azure Policy et le routage d’applications HTTP. Des travaux sont en cours pour supprimer la dépendance des modules complémentaires au nom de principal du service (SPN). L’objectif est de supprimer entièrement la nécessité d’avoir un SPN dans AKS.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont disponibles en libre-service et font l’objet d’un abonnement. Les préversions sont fournies « en l’état » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service et de la garantie limitée. Les préversions AKS sont partiellement couvertes par le service clientèle dans la mesure du possible. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour plus d’informations, consultez les articles de support suivants :
>
> - [Stratégies de support AKS](support-policies.md)
> - [FAQ du support Azure](faq.md)

## <a name="before-you-begin"></a>Avant de commencer

Les ressources suivantes doivent être installées :

- Azure CLI version 2.0.70 ou ultérieure
- Extension aks-preview 0.4.14

Pour installer l’extension aks-preview 0.4.14 ou ultérieure, utilisez les commandes Azure CLI suivantes :

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> Une fois que vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez pas actuellement la désinscrire. Quand vous activez des fonctionnalités d’évaluation, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés par la suite dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez plutôt un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Quelques minutes peuvent être nécessaires pour que l’état **Inscrit** s’affiche. Vous pouvez vérifier l’état de l’inscription à l’aide de la commande [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Quand l’état indique Inscrit, actualisez l’inscription du fournisseur de ressources `Microsoft.ContainerService` à l’aide de la commande [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

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

Obtenez enfin les informations d’identification pour accéder au cluster :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

La création du cluster prend quelques minutes. Vous pouvez ensuite déployer vos charges de travail d’application sur le nouveau cluster et interagir avec celui-ci comme vous le faisiez avec les clusters AKS basés sur le principal de service.

> [!IMPORTANT]
>
> - Les clusters AKS avec des identités managées ne peuvent être activés que pendant la création du cluster.
> - Il est impossible de mettre à jour ou de mettre à niveau des clusters AKS existants pour activer des identités managées.
