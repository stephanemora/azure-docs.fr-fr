---
title: Utilisation du programme de mise à l’échelle automatique de cluster d’Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser le programme de mise à l’échelle automatique de cluster pour mettre automatiquement à l’échelle votre cluster afin de répondre aux demandes applicatives d’un cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: d8e095303161002d10914ca7c3213ac0c6894e5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467123"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Preview - automatiquement à l’échelle un cluster pour répondre aux besoins de l’application sur Azure Kubernetes Service (AKS)

Pour suivre le rythme des demandes applicatives d’Azure Kubernetes Service (ACS), vous devrez peut-être ajuster le nombre de nœuds qui exécutent vos charges de travail. Le composant programme de mise à l’échelle automatique de cluster peut surveiller les pods de votre cluster qui ne peuvent pas être planifiés en raison de contraintes de ressources. Lorsque des problèmes sont détectés, le nombre de nœuds est augmenté pour répondre à la demande applicative. Les pods exécutés sont également régulièrement vérifiés sur les nœuds dont le nombre est réduit au besoin. Cette possibilité d’augmenter ou de réduire automatiquement le nombre de nœuds dans votre cluster AKS vous permet d’exécuter un cluster efficace et économique.

Cet article vous montre comment activer et gérer le programme de mise à l’échelle automatique de cluster dans un cluster AKS.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont libre-service et participer. Les préversions sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Toutefois, ils ne sont pas pris en charge par le support technique Azure. Si vous créez un cluster, ou ajoutez ces fonctionnalités à des clusters existants, ce cluster est non pris en charge jusqu'à ce que la fonctionnalité n’est plus disponible en version préliminaire et atteignent à la disposition générale (GA).
>
> Si vous rencontrez des problèmes avec les fonctionnalités en version préliminaire, [de signaler un problème sur le référentiel GitHub d’AKS][aks-github] par le nom de la fonctionnalité d’aperçu dans le titre du bogue.

## <a name="before-you-begin"></a>Avant de commencer

Pour les besoins de cet article, vous devez utiliser Azure CLI version 2.0.55 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Les clusters AKS qui prennent en charge le programme de mise à l’échelle automatique de cluster doivent utiliser des groupes de machines virtuelles identiques et exécuter Kubernetes *1.12.4* ou ultérieur. Cette prise en charge des groupes identiques est en préversion. Pour accepter et créer des clusters utilisant des groupes identiques, installez tout d’abord l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Si vous avez installé précédemment le *aks-preview* extension, installez toutes des mises à jour à l’aide de la `az extension update --name aks-preview` commande.

### <a name="register-scale-set-feature-provider"></a>Inscrire le fournisseur de fonctionnalités de groupe identique

Pour créer un AKS qui utilise des groupes identiques, vous devez également activer un indicateur de fonctionnalité sur votre abonnement. Pour enregistrer l’indicateur de fonctionnalité *VMSSPreview*, utilisez la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l'état de l'enregistrement à l'aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="about-the-cluster-autoscaler"></a>À propos du programme de mise à l’échelle automatique de cluster

Pour faire face à l’évolution des demandes applicatives, par exemple entre les journées de travail et les soirées ou pendant les week-ends, les clusters ont souvent besoin d’être mis à l’échelle automatiquement. Les clusters AKS peuvent être mis à l’échelle de deux manières :

* Le **programme de mise à l’échelle automatique de cluster** surveille les pods qui ne peuvent pas être planifiés sur les nœuds en raison de contraintes de ressources. Le cluster augmente alors automatiquement le nombre de nœuds.
* Le **programme de mise à l’échelle automatique de pods élastique** utilise le serveur de mesures d’un cluster Kubernetes pour surveiller la demande en ressources de pods. Si un service a besoin de davantage de ressources, le nombre de pods est automatiquement augmenté pour répondre à la demande.

![Le programme de mise à l’échelle automatique de cluster et le programme de mise à l’échelle automatique de pods élastique fonctionnent souvent ensemble pour assurer la prise en charge des demandes applicatives requises.](media/autoscaler/cluster-autoscaler.png)

Ces deux programmes de mise à l’échelle automatique peuvent également réduire le nombre de pods et de nœuds en fonction des besoins. Le programme de mise à l’échelle automatique de cluster diminue le nombre de nœuds en cas de capacité inutilisée pendant une période de temps. Les pods d’un nœud à supprimer par le programme de mise à l’échelle automatique de cluster sont planifiés en toute sécurité ailleurs dans le cluster. Le programme de mise à l’échelle automatique de cluster peut être incapable de réduire le nombre de nœuds si des pods ne peuvent pas être déplacés, notamment dans les situations suivantes :

* Un pod est créé directement et n’est pas pris en charge par un objet de contrôleur, par exemple un déploiement ou un jeu de réplicas.
* Un budget d’interruption de pods est trop restrictif et n’autorise pas la diminution du nombre de pods au-dessous d’un certain seuil.
* Un pod utilise des sélecteurs de nœud ou l’anti-affinité qui ne peuvent pas être respectés s’ils sont planifiés sur un autre nœud.

Pour plus d’informations sur l’incapacité du programme de mise à l’échelle automatique de cluster à réduire le nombre de nœuds, consultez l’article [What types of pods can prevent the cluster autoscaler from removing a node?][autoscaler-scaledown] (Quels sont les types de pods pouvant empêcher la suppression d’un nœud par le programme de mise à l’échelle automatique de cluster ?).

Le programme de mise à l’échelle automatique de cluster utilise des paramètres de démarrage pour des éléments tels que les intervalles de temps entre les événements de mise à l’échelle et les seuils de ressources. Ces paramètres sont définis par la plateforme Azure et ne sont pas exposés actuellement pour que vous les ajustiez. Pour plus d’informations sur les paramètres utilisés par le programme de mise à l’échelle automatique de cluster, consultez l’article [What are the cluster autoscaler parameters?][autoscaler-parameters] (Quels sont les paramètres du programme de mise à l’échelle automatique de cluster ?).

Les deux programmes de mise à l’échelle automatique peuvent fonctionner ensemble et sont souvent déployés tous les deux dans un cluster. Lorsqu’ils sont combinés, le programme de mise à l’échelle automatique de pods élastique se concentre sur l’exécution du nombre de pods requis pour répondre à la demande applicative. Le programme de mise à l’échelle automatique de cluster est axé sur l’exécution du nombre de nœuds nécessaires pour assurer la prise en charge des pods planifiés.

> [!NOTE]
> La mise à l’échelle manuelle est désactivée lorsque vous utilisez le programme de mise à l’échelle automatique de cluster. Laissez le programme de mise à l’échelle automatique de cluster déterminer le nombre de nœuds requis. Si vous souhaitez mettre à l’échelle manuellement votre cluster, [désactivez le programme de mise à l’échelle automatique de cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Création d’un cluster AKS et activation du programme de mise à l’échelle automatique de cluster

Utilisez la commande [az aks create][az-aks-create] pour créer un cluster AKS. Spécifiez un paramètre *--kubernetes-version* égal ou supérieur au numéro de version minimale requis indiqué dans la section précédente [Avant de commencer](#before-you-begin). Pour activer et configurer le programme de mise à l’échelle automatique de cluster, utilisez le paramètre *--enable-cluster-autoscaler*, puis spécifiez un *--min-count* et un *--max-count* de nœuds.

> [!IMPORTANT]
> L’autoscaler de cluster est un composant Kubernetes. Bien que le cluster AKS utilise un groupe de machines virtuelles identiques défini pour les nœuds, n’activez pas ou n’éditez pas manuellement les paramètres pour la mise à l’échelle automatique du groupe de machines virtuelles identiques dans le portail Azure ou en utilisant l’interface de ligne de commande Azure. Laissez le programme de mise à l’échelle automatique de cluster Kubernetes gérer les paramètres de mise à l’échelle requis. Pour plus d’informations, voir [Puis-je modifier les ressources AKS dans le groupe de ressources MC_ ?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc_-resource-group)

L’exemple suivant illustre la création d’un cluster AKS avec un groupe de machines virtuelles identiques et le programme de mise à l’échelle automatique de cluster activé. Il utilise un minimum de *1* nœud et un maximum de *3* nœuds :

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.6 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

La création du cluster et la configuration des paramètres du programme de mise à l’échelle automatique de cluster prennent quelques minutes.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Activation du programme de mise à l’échelle automatique de cluster sur un cluster AKS existant

Vous pouvez activer le programme de mise à l’échelle automatique de cluster sur un cluster AKS existant qui répond aux exigences décrites dans la section précédente [Avant de commencer](#before-you-begin). Utilisez la commande [az aks update][az-aks-update] et choisissez *--enable-cluster-autoscaler*, puis spécifiez un *--min-count* et un *--max-count* de nœuds. L’exemple suivant illustre l’activation du programme de mise à l’échelle automatique de cluster sur un cluster existant utilisant un minimum de *1* nœud et un maximum de *3* nœuds :

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Si le nombre minimal de nœuds est supérieur au nombre de nœuds existant dans le cluster, la création des nœuds supplémentaires prend quelques minutes.

## <a name="change-the-cluster-autoscaler-settings"></a>Modification des paramètres du programme de mise à l’échelle automatique de cluster

Dans l’étape précédente de création ou de mise à jour d’un cluster AKS existant, le nombre minimal de nœuds du programme de mise à l’échelle automatique de cluster a été défini sur *1* tandis que le nombre maximal de nœuds a été défini sur *3*. Si vos demandes applicatives changent, vous devrez peut-être ajuster le nombre de nœuds du programme de mise à l’échelle automatique de cluster.

Pour modifier le nombre de nœuds, utilisez la commande [az aks update][az-aks-update] et spécifiez des valeurs minimale et maximale. Dans l’exemple suivant, le *--min-count* est défini sur *1* et le *--max-count* est défini sur *5* :

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> Pendant la préversion, vous ne pouvez pas définir un nombre de nœuds minimal supérieur au nombre actuellement défini pour le cluster. Par exemple, si le nombre minimal est actuellement défini sur *1* pour le cluster, vous ne pouvez pas modifier le nombre minimal pour le définir sur *3*.

Surveillez les performances de vos applications et services, puis ajustez les nombres de nœuds du programme de mise à l’échelle automatique de cluster pour les faire correspondre aux performances requises.

## <a name="disable-the-cluster-autoscaler"></a>Désactivation du programme de mise à l’échelle automatique de cluster

Si vous ne souhaitez plus utiliser le programme de mise à l’échelle automatique de cluster, vous pouvez le désactiver à l’aide de la commande [az aks update][az-aks-update]. Les nœuds ne sont pas supprimés lorsque le programme de mise à l’échelle automatique de cluster est désactivé.

Pour supprimer le programme de mise à l’échelle automatique de cluster, spécifiez le paramètre *--disable-cluster-autoscaler*, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Vous pouvez mettre à l’échelle manuellement votre cluster à l’aide de la commande [az aks scale][az-aks-scale]. Si vous utilisez le programme de mise à l’échelle automatique de pods élastique, cette fonctionnalité continue de s’exécuter avec le programme de mise à l’échelle automatique de cluster désactivé, mais la planification des pods peut ne plus être possible si l’ensemble des ressources de nœuds sont utilisées.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment mettre automatiquement à l’échelle le nombre de nœuds AKS. Vous pouvez également utiliser le programme de mise à l’échelle automatique de pods élastique pour ajuster automatiquement le nombre de pods qui exécutent votre application. Pour obtenir des instructions sur l’utilisation du programme de mise à l’échelle automatique de pods élastique, consultez l’article [Didacticiel : Mettre à l’échelle des applications dans Azure Kubernetes Service (AKS)][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-github]: https://github.com/azure/aks/issues

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
