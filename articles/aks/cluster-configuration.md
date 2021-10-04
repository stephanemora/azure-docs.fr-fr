---
title: Configuration de clusters dans Azure Kubernetes Service (AKS)
description: Découvrez comment configurer un cluster dans Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 6c454c23eec0bb5b0fef1ceca3ad8f8e4c52493d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812598"
---
# <a name="configure-an-aks-cluster"></a>Configurer un cluster AKS

Dans le cadre de la création d’un cluster AKS, vous devrez peut-être personnaliser la configuration de votre cluster en fonction de vos besoins. Cet article présente quelques options pour personnaliser votre cluster AKS.

## <a name="os-configuration"></a>Configuration du système d’exploitation

AKS prend désormais en charge Ubuntu 18.04 comme système d’exploitation (SE) du nœud par défaut en disponibilité générale (GA) pour les clusters.

## <a name="container-runtime-configuration"></a>Configuration du runtime de conteneur

Un runtime de conteneur est un logiciel qui exécute des conteneurs et gère des images conteneur sur un nœud. Le runtime permet d’abstraire les appels système ou les fonctionnalités spécifiques au système d’exploitation pour exécuter des conteneurs sous Linux ou Windows. Pour les pools de nœuds Linux, `containerd` est utilisé pour les pools de nœuds utilisant Kubernetes version 1.19 et ultérieure. Pour des pools de nœuds Windows Server 2019, `containerd` est disponible en préversion et peut être utilisé dans les pools de nœuds utilisant Kubernetes version 1.20 ou ultérieure, mais Docker est toujours utilisé par défaut.

[`Containerd`](https://containerd.io/) est un runtime de conteneur principal conforme à la norme [OCI](https://opencontainers.org/) (Open Container Initiative) qui fournit l’ensemble minimal de fonctionnalités requises pour exécuter des conteneurs et gérer des images sur un nœud. Il a été [remis gracieusement](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) à la Cloud Native Compute Foundation (CNCF) en mars 2017. La version actuelle de Moby (Docker amont) qu’utilise AKS s’appuie déjà sur `containerd`, comme indiqué ci-dessus.

Grâce à un nœud et à des pools de nœuds `containerd`, au lieu de communiquer avec le `dockershim`, le kubelet communique directement avec `containerd` via le plug-in CRI (interface du runtime de conteneur), ce qui supprime les tronçons additionnels sur le flux par rapport à l’implémentation de la CRI Docker. Ainsi, vous constaterez une meilleure latence au démarrage du pod et une moindre utilisation des ressources (processeur et mémoire).

En utilisant `containerd` pour les nœuds AKS, la latence au démarrage du pod s’améliore et la consommation des ressources de nœud par le runtime de conteneur diminue. Ces améliorations sont rendues possibles grâce à cette nouvelle architecture dans laquelle le kubelet communique directement avec `containerd` via le plug-in CRI. Dans l’architecture Moby ou Docker, le kubelet communiquerait avec le `dockershim` et le moteur Docker avant d’atteindre `containerd`, ce qui donne lieu à des tronçons additionnels sur le flux.

![CRI Docker 2](media/cluster-configuration/containerd-cri.png)

`Containerd` fonctionne sur chaque version GA de Kubernetes dans AKS et dans chaque version de Kubernetes en amont ultérieure à v1.19 et prend en charge toutes les fonctionnalités Kubernetes et AKS.

> [!IMPORTANT]
> Les clusters avec des pools de nœuds Linux créés sur Kubernetes version 1.19 ou ultérieure ont par défaut `containerd` comme runtime de conteneur. Les clusters avec des pools de nœuds sur des versions antérieures de Kubernetes prises en charge reçoivent Docker comme runtime de conteneur. Les pools de nœuds Linux sont mis à jour en `containerd` une fois la version de Kubernetes du pool de nœuds mise à jour vers une version prenant en charge `containerd`. Vous pouvez toujours utiliser des clusters et pools de nœuds Docker sur des versions antérieures tant qu’elles sont prises en charge.
> 
> L’utilisation de `containerd` avec des pools de nœuds Windows Server 2019 est actuellement en préversion. Pour plus d’informations, consultez [Ajouter un pool de nœuds Windows Server avec `containerd`][aks-add-np-containerd].
> 
> Il est vivement recommandé de tester vos charges de travail sur des pools de nœuds AKS avec `containerd` avant d’utiliser des clusters avec une version de Kubernetes prenant en charge `containerd` pour vos pools de nœuds.

### <a name="containerd-limitationsdifferences"></a>Limitations/différences de `Containerd`

* Pour `containerd`, nous vous recommandons d’utiliser [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) comme interface CLI de remplacement au lieu de l’interface de ligne de commande Docker pour **résoudre des problèmes** relatifs aux pods, aux containers et aux images conteneur sur les nœuds Kubernetes (par exemple, `crictl ps`). 
   * Elle n’offre pas toutes les fonctionnalités de l’interface de ligne de commande Docker. Elle est uniquement destinée à la résolution des problèmes.
   * `crictl` offre une vue des conteneurs plus adaptée à Kuberbetes, avec des concepts tels que les pods, etc.
* `Containerd` configure la journalisation en utilisant le format de journalisation `cri` standardisé (qui diffère de ce que vous obtenez actuellement du pilote JSON de Docker). Votre solution de journalisation doit prendre en charge le format de journalisation `cri` (par exemple, [Azure Monitor pour les conteneurs](../azure-monitor/containers/container-insights-enable-new-cluster.md)).
* Vous ne pouvez plus accéder au moteur Docker, `/var/run/docker.sock`, ni utiliser Docker-in-Docker (DinD).
  * Si vous extrayez actuellement des journaux d’applications ou des données de surveillance à partir du moteur Docker, utilisez plutôt une fonctionnalité telle qu’[Azure Monitor pour les conteneurs](../azure-monitor/containers/container-insights-enable-new-cluster.md). En outre, AKS ne prend pas en charge l’exécution de commandes hors bande sur les nœuds d’agent qui pourraient provoquer une instabilité.
  * Même en cas d’utilisation de Docker, il est fortement déconseillé de créer des images et d’utiliser directement le moteur Docker via les méthodes ci-dessus. Kubernetes ne connaît pas pleinement les ressources consommées, et ces approches présentent de nombreux problèmes détaillés [ici](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) et [ici](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), par exemple.
* Création d’images : vous pouvez continuer à utiliser votre workflow de génération Docker actuel comme d’habitude, sauf si vous générez des images dans votre cluster AKS. Dans ce cas, envisagez de passer à l’approche recommandée pour la création d’images à l’aide de [tâches ACR](../container-registry/container-registry-quickstart-task-cli.md) ou d’une option de cluster plus sécurisée comme [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Ordinateurs virtuels de génération 2

Azure prend en charge les [machines virtuelles de 2e génération (Gen2)](../virtual-machines/generation-2.md). Les machines virtuelles de 2e génération prennent en charge des fonctionnalités clés qui ne sont pas prises en charge par les machines virtuelles de 1re génération (Gen1). Ces fonctionnalités incluent la mémoire augmentée, Intel Software Guard Extensions (Intel SGX) et mémoire persistante virtualisée (vPMEM).

Les machines virtuelles de 2e génération utilisent la nouvelle architecture de démarrage basée sur UEFI alors que les machines virtuelles de 1ère génération utilisaient l’architecture basée sur le BIOS.
Seules des références SKU et des tailles spécifiques prennent en charge les machines virtuelles Gen2. Vérifiez la [liste des tailles prises en charge](../virtual-machines/generation-2.md#generation-2-vm-sizes) pour voir si votre référence SKU prend en charge ou requiert Gen2.

En outre, toutes les images de machine virtuelle ne prennent pas en charge Gen2. Sur AKS, les machines virtuelles Gen2 utiliseront la nouvelle [image AKS Ubuntu 18.04](#os-configuration). Cette image prend en charge toutes les tailles et références SKU de Gen2.

## <a name="ephemeral-os"></a>Système d’exploitation éphémère

Par défaut, Azure réplique automatiquement le disque de système d’exploitation d’une machine virtuelle dans Stockage Azure pour éviter toute perte de données si la machine virtuelle doit être déplacée vers un autre hôte. Toutefois, comme les conteneurs ne sont pas conçus pour conserver l’état local, ce comportement offre une valeur limitée tout en présentant certains inconvénients, notamment un approvisionnement plus lent des nœuds et une latence en lecture/écriture plus élevée.

En revanche, les disques de système d’exploitation éphémères sont stockés uniquement sur l’ordinateur hôte, comme un disque temporaire. Cela permet de réduire la latence en lecture/écriture, tout en accélérant la mise à l’échelle des nœuds et la mise à niveau des clusters.

À l’instar du disque temporaire, un disque de système d’exploitation éphémère est inclus dans le prix de la machine virtuelle, ce qui signifie que vous n’encourez aucun coût de stockage supplémentaire.

> [!IMPORTANT]
>Quand un utilisateur ne demande pas explicitement de disques managés pour le système d’exploitation, si possible, AKS utilise par défaut le système d’exploitation éphémère pour une configuration de pool de nœuds donnée.

Lorsque vous utilisez le système d’exploitation éphémère, le disque de système d’exploitation doit tenir dans le cache de la machine virtuelle. Les tailles du cache de la machine virtuelle sont disponibles dans la [documentation Azure](../virtual-machines/dv3-dsv3-series.md) entre parenthèses à côté du débit d’E/S (« taille du cache en Gio »).

En prenant comme exemple la taille de machine virtuelle AKS par défaut Standard_DS2_v2 avec la taille de disque de système d’exploitation par défaut de 100 Go, cette taille de machine virtuelle prend en charge le système d’exploitation éphémère mais n’a que 86 Go de taille de cache. Cette configuration utile par défaut des disques managés si l’utilisateur ne le spécifie pas explicitement. Si un utilisateur a explicitement demandé le système d’exploitation éphémère, il reçoit une erreur de validation.

Si un utilisateur demande la même référence Standard_DS2_v2 avec un disque de système d’exploitation de 60 Go, la configuration utilise par défaut le système d’exploitation éphémère : la taille demandée de 60 Go est inférieure à la taille de cache maximale de 86 Go.

En utilisant la référence Standard_D8s_v3 avec un disque de système d’exploitation de 100 Go, cette taille de machine virtuelle prend en charge le système d’exploitation éphémère et dispose de 200 Go d’espace de cache. Si un utilisateur ne spécifie pas le type de disque de système d’exploitation, le pool de nœuds reçoit le système d’exploitation éphémère par défaut. 

Le système d’exploitation éphémère nécessite au moins la version 2.15.0 de l’interface de ligne de commande Azure.

### <a name="use-ephemeral-os-on-new-clusters"></a>Utiliser un système d’exploitation éphémère sur les nouveaux clusters

Configurez le cluster pour qu’il utilise des disques de système d’exploitation éphémères lors de sa création. Utilisez l’indicateur `--node-osdisk-type` pour définir le système d’exploitation éphémère comme type de disque de système d’exploitation pour le nouveau cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Si vous souhaitez créer un cluster ordinaire à l’aide de disques de système d’exploitation attachés au réseau, vous pouvez le faire en spécifiant `--node-osdisk-type=Managed`. Vous pouvez également choisir d’ajouter des pools de nœuds de système d’exploitation éphémère comme indiqué ci-dessous.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Utiliser un système d’exploitation éphémère sur les clusters existants
Configurez un nouveau pool de nœuds pour utiliser les disques de système d’exploitation éphémères. Utilisez l’indicateur `--node-osdisk-type` pour définir le système d’exploitation éphémère comme type de disque de système d’exploitation pour ce pool de nœuds.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Grâce au système d’exploitation éphémère, vous pouvez déployer des images de machine virtuelle et d’instance jusqu’au maximum de la capacité du cache de la machine virtuelle. Dans le cas d’AKS, la configuration du disque de système d’exploitation du nœud par défaut utilise 128 Go, ce qui signifie que vous avez besoin d’une taille de machine virtuelle dont le cache est supérieur à 128 Go. La référence Standard_DS2_v2 par défaut a une taille de cache de 86 Go, ce qui n’est pas suffisant. La référence Standard_DS3_v2 a une taille de cache de 172 Go, ce qui est suffisant. Vous pouvez également réduire la taille par défaut du disque du système d’exploitation en utilisant `--node-osdisk-size`. La taille minimale des images AKS est 30 Go. 

Si vous souhaitez créer des pools de nœuds avec des disques de système d’exploitation attachés au réseau, vous pouvez le faire en spécifiant `--node-osdisk-type Managed`.

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

- Découvrez comment [mettre à niveau les images de nœud](node-image-upgrade.md) dans votre cluster.
- Pour plus d’informations sur la mise à niveau de votre cluster vers la dernière version de Kubernetes, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)](upgrade-cluster.md).
- En savoir plus sur [`containerd` et Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Consultez la liste des [questions fréquentes sur AKS](faq.md) pour trouver des réponses à certaines questions courantes concernant AKS.
- En savoir plus sur les [disques de système d’exploitation éphémères](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview