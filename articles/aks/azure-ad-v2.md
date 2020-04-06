---
title: Utiliser Azure AD dans Azure Kubernetes Service
description: Découvrez comment utiliser Azure AD dans Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 1ca4b70139ed5e0a136f6f5f2b0382b8c1688983
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389407"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Intégrer Azure AD à Azure Kubernetes Service (préversion)

> [!Note]
> Les clusters AKS v1 existants avec une intégration AD ne sont pas concernés par la nouvelle expérience AKS v2.

L’intégration d’Azure AD avec AKS v2 est conçue pour simplifier l’intégration d’Azure AD à l’expérience AKS v1, où les utilisateurs devaient créer une application cliente et une application serveur et où le locataire Azure AD devait accorder des autorisations de lecture du répertoire. Dans la nouvelle version, le fournisseur de ressources AKS gère les applications cliente et serveur pour vous.

## <a name="limitations"></a>Limites

* Vous ne pouvez pas actuellement mettre à niveau un cluster AKS v1 compatible avec Azure AD vers l’expérience v2.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont disponibles en libre-service et font l’objet d’un abonnement. Les préversions sont fournies « en l’état » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service et de la garantie limitée. Les préversions AKS sont, dans la mesure du possible, partiellement couvertes par le service clientèle. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour plus d’informations, consultez les articles de support suivants :
>
> - [Stratégies de support AKS](support-policies.md)
> - [FAQ du support Azure](faq.md)

## <a name="before-you-begin"></a>Avant de commencer

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.2.0 ou ultérieure
- Extension aks-preview 0.4.38
- Kubectl avec une version minimale [1.18 bêta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

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
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
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
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
Si vous créez d’abord un groupe et que vous ajoutez des membres, vous pouvez également activer le groupe Azure AD au moment de la création à l’aide de la commande suivante :

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Lorsqu’un cluster v2 Azure AD a bien été créé, vous trouvez la section suivante dans le corps de la réponse.
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

En savoir plus sur le [Contrôle d’accès en fonction du rôle Azure AD][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
