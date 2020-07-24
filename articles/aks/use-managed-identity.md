---
title: Utiliser les identités managées dans Azure Kubernetes Service
description: Découvrez comment utiliser les identités managées dans Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.topic: article
ms.date: 07/10/2020
ms.author: mlearned
ms.openlocfilehash: 95a303a4b6a83901560b26679bca920b9de4d3f4
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250903"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Utiliser les identités managées dans Azure Kubernetes Service

Actuellement, un cluster Azure Kubernetes Service ou AKS (plus précisément, le fournisseur cloud Kubernetes) nécessite une identité pour créer des ressources supplémentaires telles que des équilibreurs de charge et des disques managés dans Azure. Cette identité peut être une *identité gérée* ou un *principal de service*. Si vous utilisez un [principal de service](kubernetes-service-principal.md), vous devez en fournir un ; sinon, AKS en crée un en votre nom. Si vous utilisez une identité managée, elle est automatiquement créée pour vous par AKS. Les clusters utilisant des principaux de service finissent par atteindre un état dans lequel le principal de service doit être renouvelé pour que le cluster continue de fonctionner. La gestion des principaux de service ajoute de la complexité : c’est pourquoi il est plus facile d’utiliser à la place des identités managées. Les mêmes exigences d’autorisation s’appliquent aux principaux de service et aux identités managées.

Les *identités managées* correspondent essentiellement à un wrapper autour des principaux de service, ce qui simplifie leur gestion. La rotation des informations d’identification pour MI se produit automatiquement tous les 46 jours selon la valeur par défaut dans Azure Active Directory. AKS utilise aussi bien les identités managées affectées par le système que les types d’identités managées affectées par l’utilisateur. Ces identités sont actuellement immuables. Pour en savoir plus, découvrez les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Avant de commencer

La ressource suivante doit être installée :

- Azure CLI 2.8.0 (ou une version ultérieure)

## <a name="limitations"></a>Limites

* L’apport de vos propres identités managées n’est pas pris en charge actuellement.
* Les clusters AKS avec des identités managées ne peuvent être activés que pendant la création du cluster.
* Il est impossible de mettre à jour ou de mettre à niveau des clusters AKS existants pour activer des identités managées.
* Pendant les opérations de **mise à niveau** du cluster, l’identité managée est temporairement indisponible.

## <a name="summary-of-managed-identities"></a>Résumé des identités managées

AKS utilise plusieurs identités managées pour les services intégrés et les modules complémentaires.

| Identité                       | Nom    | Cas d’utilisation | Autorisations par défaut | Apportez votre propre identité
|----------------------------|-----------|----------|
| Plan de contrôle | non visible | Utilisé par AKS pour gérer les ressources de mise en réseau, par exemple la création d’un équilibreur de charge pour l’entrée, l’adresse IP publique, etc.| Rôle Contributeur pour le groupe de ressources du nœud | Non prise en charge pour le moment
| Kubelet | Nom du cluster AKS - agentpool | Authentification avec Azure Container Registry (ACR) | Rôle Lecteur pour le groupe de ressources du nœud | Non prise en charge pour le moment
| Composant additionnel | AzureNPM | Aucune identité requise | N/D | Non
| Composant additionnel | Analyse du réseau AzureCNI | Aucune identité requise | N/D | Non
| Composant additionnel | azurepolicy (gatekeeper) | Aucune identité requise | N/D | Non
| Composant additionnel | azurepolicy | Aucune identité requise | N/D | Non
| Composant additionnel | Calico | Aucune identité requise | N/D | Non
| Composant additionnel | tableau de bord | Aucune identité requise | N/D | Non
| Composant additionnel | HTTPApplicationRouting | Gère les ressources réseau requises | Rôle Lecteur pour le groupe de ressources du nœud, rôle Contributeur pour la zone DNS | Non
| Composant additionnel | Passerelle d'application d’entrée | Gère les ressources réseau requises| Rôle Contributeur pour le groupe de ressources du nœud | Non
| Composant additionnel | omsagent | Utilisé pour envoyer des métriques AKS à Azure Monitor | Rôle Éditeur des métriques de surveillance | Non
| Composant additionnel | Nœud virtuel (ACIConnector) | Gère les ressources réseau requises pour Azure Container Instances (ACI) | Rôle Contributeur pour le groupe de ressources du nœud | Non


## <a name="create-an-aks-cluster-with-managed-identities"></a>Créer un cluster AKS avec des identités managées

Vous pouvez désormais créer un cluster AKS avec des identités managées à l’aide des commandes CLI suivantes.

Commencez par créer un groupe de ressources Azure :

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Créez ensuite un cluster AKS :

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Un cluster créé à l’aide d’identités managées contient les informations de profil du principal de service suivantes :

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Utilisez la commande suivante pour interroger l’objectid de votre identité managée de plan de contrôle :

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

Le résultat doit avoir l’aspect suivant :

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> Pour créer et utiliser votre propre réseau virtuel, une adresse IP statique ou un disque Azure attaché où les ressources se trouvent en dehors du groupe de ressources du nœud Worker, utilisez le PrincipalID du cluster Identité managée affectée par le système pour effectuer une attribution de rôle. Pour plus d’informations sur l’attribution de rôle, consultez [Déléguer l’accès à d’autres ressources Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> L'octroi d'une autorisation à une identité managée en cluster utilisée par le fournisseur Azure Cloud peut prendre jusqu'à 60 minutes.

Obtenez enfin les informations d’identification pour accéder au cluster :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

La création du cluster prend quelques minutes. Vous pouvez ensuite déployer vos charges de travail d’application sur le nouveau cluster et interagir avec celui-ci comme vous le faisiez avec les clusters AKS basés sur le principal de service.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez des [modèles ARM (Azure Resource Manager)][aks-arm-template] pour créer des clusters avec gestion des identités.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
