---
title: Utiliser Azure AD dans Azure Kubernetes Service
description: Découvrez comment utiliser Azure AD dans Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204376"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Intégrer Azure AD à Azure Kubernetes Service (préversion)

> [!Note]
> Les clusters AKS existants avec intégration AAD (Azure Active Directory) ne sont pas affectés par la nouvelle expérience AAD gérée par AKS.

L’intégration d’Azure AD avec AAD géré par AKS est conçue pour simplifier l'expérience d'intégration Azure AD, où les utilisateurs devaient auparavant créer une application cliente et une application serveur et où le locataire Azure AD devait accorder des autorisations de lecture du répertoire. Dans la nouvelle version, le fournisseur de ressources AKS gère les applications cliente et serveur pour vous.

## <a name="limitations"></a>Limites

* Actuellement, vous ne pouvez pas mettre à niveau un cluster AKS intégré à AAD existant vers la nouvelle expérience AAD gérée par AKS.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont disponibles en libre-service et font l’objet d’un abonnement. Les préversions sont fournies « en l’état » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service et de la garantie limitée. Les préversions AKS sont, dans la mesure du possible, partiellement couvertes par le service clientèle. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour plus d’informations, consultez les articles de support suivants :
>
> - [Stratégies de support AKS](support-policies.md)
> - [FAQ du support Azure](faq.md)

## <a name="before-you-begin"></a>Avant de commencer

> [!Important]
> Vous devez utiliser Kubectl avec une version minimale 1.18

Les ressources suivantes doivent être installées :

- Azure CLI version 2.5.1 ou ultérieure
- Extension aks-preview 0.4.38
- Kubectl avec une version minimale [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Pour installer ou mettre à jour l’extension aks-preview 0.4.38 ou ultérieure, utilisez les commandes Azure CLI suivantes :

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Pour installer kubectl, utilisez la commande suivante :

```azurecli
sudo az aks install-cli
kubectl version --client
```

Pour d’autres systèmes d’exploitation, utilisez [ces instructions](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

> [!CAUTION]
> Une fois que vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez pas actuellement la désinscrire. Quand vous activez des fonctionnalités d’évaluation, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés par la suite dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez plutôt un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Quelques minutes peuvent être nécessaires pour que l’état **Inscrit** s’affiche. Vous pouvez vérifier l’état de l’inscription à l’aide de la commande [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quand l’état indique Inscrit, actualisez l’inscription du fournisseur de ressources `Microsoft.ContainerService` à l’aide de la commande [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Créer un cluster AKS compatible Azure AD

Vous pouvez désormais créer un cluster AKS à l’aide des commandes CLI suivantes.

Commencez par créer un groupe de ressources Azure :

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Créez ensuite un cluster AKS :

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
La commande ci-dessus crée un cluster AKS à trois nœuds, mais l’utilisateur qui a créé le cluster, par défaut, n’est pas membre d’un groupe qui a accès à ce cluster. Cet utilisateur doit créer un groupe Azure AD, s’ajouter comme membre du groupe, puis mettre à jour le cluster comme indiqué ci-dessous. Suivez les instructions [ici](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

Une fois que vous avez créé un groupe et que vous vous êtes ajouté (et avez ajouté d’autres personnes) en tant que membre, vous pouvez mettre à jour le cluster avec le groupe Azure AD à l’aide de la commande suivante :

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Si vous créez d’abord un groupe et que vous ajoutez des membres, vous pouvez également activer le groupe Azure AD au moment de la création à l’aide de la commande suivante :

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Lorsqu’un cluster AAD géré par AKS a bien été créé, vous trouvez la section suivante dans le corps de la réponse.
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

La création du cluster prend quelques minutes.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accéder à un cluster compatible Azure AD
Pour récupérer les informations d’identification d’administrateur permettant d’accéder au cluster :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Utilisez maintenant la commande kubectl get nodes pour voir les nœuds figurant dans le cluster :

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Pour récupérer les informations d’identification d’utilisateur permettant d’accéder au cluster :
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Suivez les instructions pour vous connecter.

Vous recevez le message : **Vous devez être connecté au serveur (non autorisé)**

L’utilisateur ci-dessus obtient une erreur, car il ne fait pas partie d’un groupe ayant accès au cluster.

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur le [Contrôle d’accès en fonction du rôle Azure AD][azure-ad-rbac].
* Utilisez [kubelogin](https://github.com/Azure/kubelogin) pour accéder aux fonctionnalités de l’authentification Azure non disponibles dans kubectl.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
