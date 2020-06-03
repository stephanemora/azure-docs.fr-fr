---
title: Configuration de clusters dans Azure Kubernetes Service (AKS)
description: Découvrez comment configurer un cluster dans Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725144"
---
# <a name="configure-an-aks-cluster"></a>Configurer un cluster AKS

Dans le cadre de la création d’un cluster AKS, vous devrez peut-être personnaliser la configuration de votre cluster en fonction de vos besoins. Cet article présente quelques options pour personnaliser votre cluster AKS.

## <a name="os-configuration-preview"></a>Configuration du système d’exploitation (préversion)

AKS prend désormais en charge Ubuntu 18.04 comme système d’exploitation de nœud (en préversion). Dans la préversion, Ubuntu 16.04 et Ubuntu 18.04 seront tous les deux disponibles.

Les ressources suivantes doivent être installées :

- Azure CLI version 2.2.0 ou ultérieure
- L’extension aks-preview 0.4.35

Pour installer l’extension aks-preview 0.4.35 ou version ultérieure, utilisez les commandes Azure CLI suivantes :

```azurecli
az extension add --name aks-preview
az extension list
```

Inscrivez la fonctionnalité `UseCustomizedUbuntuPreview` :

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Quelques minutes peuvent être nécessaires pour que l’état **Inscrit** s’affiche. Vous pouvez vérifier l’état de l’inscription à l’aide de la commande [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quand l’état indique Inscrit, actualisez l’inscription du fournisseur de ressources `Microsoft.ContainerService` à l’aide de la commande [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Nouveaux clusters

Configurez le cluster pour qu’il utilise Ubuntu 18.04 lors de sa création. Utilisez l’indicateur `--aks-custom-headers` pour définir le système d’exploitation Ubuntu 18.04 comme système d’exploitation par défaut.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Si vous souhaitez créer un cluster Ubuntu 16.04 standard, vous pouvez omettre la balise `--aks-custom-headers` personnalisée.

### <a name="existing-clusters"></a>Clusters existants

Configurez un nouveau pool de nœuds pour utiliser Ubuntu 18.04. Utilisez l’indicateur `--aks-custom-headers` pour définir le système d’exploitation Ubuntu 18.04 comme système d’exploitation par défaut pour ce pool de nœuds.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Si vous souhaitez créer un pool de nœuds Ubuntu 16.04 standard, vous pouvez omettre la balise `--aks-custom-headers` personnalisée.


## <a name="custom-resource-group-name"></a>Nom du groupe de ressources personnalisé

Lorsque vous déployez un cluster Azure Kubernetes Service dans Azure, un deuxième groupe de ressources est créé pour les nœuds Worker. Par défaut, AKS nomme le groupe de ressources de nœud `MC_resourcegroupname_clustername_location`, mais vous pouvez également choisir son nom.

Pour spécifier votre propre nom de groupe de ressources, installez la version 0.3.2 ou ultérieure de l’extension Azure CLI aks-preview. À l’aide d’Azure CLI, utilisez le paramètre `--node-resource-group` de la commande `az aks create` afin de spécifier un nom personnalisé pour le groupe de ressources. Si vous utilisez un modèle Azure Resource Manager pour déployer un cluster AKS, vous pouvez définir le nom du groupe de ressources à l’aide de la propriété `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Le groupe de ressources secondaire est automatiquement créé par le fournisseur de ressources Azure dans votre propre abonnement. Vous pouvez uniquement spécifier le nom du groupe de ressources personnalisé lors de la création du cluster. 

Lorsque vous travaillez avec le groupe de ressources de nœud, n’oubliez pas que vous ne pouvez pas :

- Spécifier un groupe de ressources existant pour le groupe de ressources de nœud.
- Spécifier un autre abonnement pour le groupe de ressources de nœud.
- Modifier le nom du groupe de ressources de nœud une fois que le cluster a été créé.
- Spécifier des noms pour les ressources managées au sein du groupe de ressources de nœud.
- Modifier ni supprimer les étiquettes des ressources managées créées par Azure au sein du groupe de ressources de nœud.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser `Kured` pour [appliquer des mises à jour de sécurité et de noyau aux nœuds Linux](node-updates-kured.md) de votre cluster.
- Pour plus d’informations sur la mise à niveau de votre cluster vers la dernière version de Kubernetes, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)](upgrade-cluster.md).
- Consultez la liste des [questions fréquentes sur AKS](faq.md) pour trouver des réponses à certaines questions courantes concernant AKS.