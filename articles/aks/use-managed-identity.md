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
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827540"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Préversion - Utiliser les identités managées dans Azure Kubernetes Service

Actuellement, les utilisateurs doivent fournir un principal de service, à défaut de quoi AKS s'en charge pour permettre au cluster AKS (plus précisément, le fournisseur cloud Kubernetes) de créer des ressources supplémentaires, telles que des équilibreurs de charge et des disques managés dans Azure. Les principaux de service sont généralement accompagnés d'une date d’expiration. Lorsqu'il atteint un certain état, le principal de service doit être renouvelé pour permettre le bon fonctionnement des clusters. Gérer les principaux de service n'est pas une mince affaire. Les identités managées correspondent essentiellement à un wrapper autour des principaux de services, ce qui simplifie leur gestion. Pour plus d'informations, consultez l'article traitant des [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS crée deux identités managées, une identité managée affectée par le système et une identité affectée par l’utilisateur. Le fournisseur de cloud Kubernetes utilise une identité managée affectée par le système afin de créer des ressources Azure pour le compte de l’utilisateur. Le cycle de vie de cette identité managée affectée par le système est lié à celui du cluster, et la suppression du cluster entraîne la suppression de l'identité. AKS crée également une identité managée affectée par l'utilisateur utilisée dans le cluster pour autoriser AKS à accéder aux enregistrements de contrôle d'accès, kubelet pour obtenir les métadonnées d’Azure, etc.

Dans cette préversion, un principal de service reste nécessaire. Il est utilisé à des fins d'autorisation des modules complémentaires tels que la surveillance, le nœud virtuel, la stratégie Azure et le routage d’applications HTTP. Nous travaillons actuellement sur la suppression de la dépendance des modules complémentaires sur le SPN et, à terme, l'exigence liée au SPN dans AKS sera complètement supprimée.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS](support-policies.md)
> * [FAQ du support Azure](faq.md)

## <a name="before-you-begin"></a>Avant de commencer

Vous devez disposer des éléments suivants :

* Le logiciel Azure CLI version 2.0.70 ou ultérieure doit également être installé, ainsi que l’extension aks-preview 0.4.14

## <a name="install-latest-aks-cli-preview-extension"></a>Installer la dernière extension de la préversion d’AKS CLI

Vous avez besoin de l’extension **aks-preview 0.4.14** ou version ultérieure.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé des fonctionnalités en préversion, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés ultérieurement dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Quelques minutes peuvent être nécessaires pour que l’état s’affiche *Inscrit*. Vous pouvez vérifier l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Une fois l'état inscrit, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>Créer un cluster AKS avec identité managée

Vous pouvez désormais créer un cluster AKS avec des identités managées à l’aide de la commande CLI suivante
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Obtenir les informations d'identification pour accéder au cluster
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Après avoir consacré quelques minutes à la création du cluster, vous pouvez déployer vos charges de travail d’application et interagir avec cette dernière comme vous le faisiez avec les clusters AKS basés sur le principal de service. 

> [!IMPORTANT]
> * Les clusters AKS avec identités managées peuvent uniquement être activés lors de la création du cluster.
> * Les clusters AKS existants ne peuvent pas être mis à jour/mis à niveau pour activer les identités managées.