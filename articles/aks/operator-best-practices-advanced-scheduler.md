---
title: Bonnes pratiques relatives aux fonctionnalités du planificateur
titleSuffix: Azure Kubernetes Service
description: Découvrir les bonnes pratiques de l’opérateur relatives à l’utilisation des fonctionnalités avancées du planificateur, notamment les teintes (taints) et tolérances (tolerations), les sélecteurs et l’affinité de nœud, ainsi que l’affinité ou l’anti-affinité entre pods dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 27b32d7d10b691ed806e4d7aa31a095630d2bfc9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103621"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Bonnes pratiques relatives aux fonctionnalités avancées du planificateur dans Azure Kubernetes Service (AKS)

Quand vous gérez des clusters dans Azure Kubernetes Service (AKS), il est souvent nécessaire d’isoler les équipes et les charges de travail. Les fonctionnalités avancées fournies par le planificateur Kubernetes vous permettent de contrôler :
* Quels pods peuvent être planifiés sur certains nœuds.
* La manière dont les applications multipods peuvent être distribuées de manière appropriée sur le cluster. 

Cet article traite des bonnes pratiques relatives aux fonctionnalités de planification Kubernetes avancées pour les opérateurs de clusters. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Utiliser des teintes (taints) et des tolérances (tolerations) pour limiter les pods pouvant être planifiés sur des nœuds.
> * Privilégier l’exécution de pods sur certains nœuds avec des sélecteurs de nœuds ou l’affinité de nœud.
> * Diviser ou regrouper des pods avec l’affinité ou l’anti-affinité entre pods.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fournir des nœuds dédiés à l’aide de teintes et de tolérances

> **Guide des meilleures pratiques :** 
>
> Limitez l’accès des applications gourmandes en ressources, comme les contrôleurs d’entrée, à des nœuds spécifiques. Conservez les ressources de nœud disponibles pour les charges de travail qui en ont besoin et n’autorisez pas la planification d’autres charges de travail sur les nœuds.

Quand vous créez votre cluster AKS, vous pouvez déployer des nœuds avec prise en charge des unités centrales graphiques (GPU) ou un grand nombre de processeurs puissants. Vous pouvez utiliser ces nœuds pour les charges de travail de traitement de données volumineuses, notamment celles liées au Machine Learning (ML) ou à l’intelligence artificielle (IA). 

Comme le déploiement de ce matériel de ressources de nœuds est généralement coûteux, limitez les charges de travail qui peuvent être planifiées sur ces nœuds. Vous devriez plutôt dédier certains nœuds du cluster à l’exécution des services d’entrée et empêcher d’autres charges de travail.

Cette prise en charge pour différents nœuds est fournie à l’aide de plusieurs pools de nœuds. Un cluster AKS fournit un ou plusieurs pools de nœud.

Le planificateur Kubernetes utilise des teintes et des tolérances pour restreindre les charges de travail qui peuvent s’exécuter sur des nœuds.

* Appliquez une **teinte** à un nœud pour indiquer que seuls des pods spécifiques peuvent être planifiés sur celui-ci.
* Appliquez ensuite une **tolérance** à un pod, lui permettant de *tolérer* la teinte d’un nœud.

Quand vous déployez un pod sur un cluster AKS, Kubernetes planifie uniquement des pods sur les nœuds dont la teinte correspond à la tolérance. Par exemple, supposons que vous ayez un pool de nœuds dans votre cluster AKS pour les nœuds avec prise en charge des GPU. Vous définissez un nom, comme *gpu*, puis une valeur pour la planification. En définissant cette valeur sur *NoSchedule*, le planificateur Kubernetes ne peut pas planifier les pods sans tolérance définie sur le nœud.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Après avoir appliqué une teinte aux nœuds, vous définirez une tolérance dans la spécification de pod qui autorise la planification sur les nœuds. L’exemple suivant définit `sku: gpu` et `effect: NoSchedule` pour tolérer la teinte appliquée au nœud à l’étape précédente :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Quand ce pod est déployé en utilisant `kubectl apply -f gpu-toleration.yaml`, Kubernetes peut planifier le pod sur les nœuds avec la teinte appliquée. Cette isolation logique vous permet de contrôler l’accès aux ressources au sein d’un cluster.

Quand vous appliquez des teintes, collaborez avec les développeurs et propriétaires d’applications pour leur permettre de définir les tolérances requises dans leurs déploiements.

Pour en savoir plus sur l’utilisation de plusieurs pools de nœuds dans AKS, voir [Créer et gérer plusieurs pools de nœuds pour un cluster dans AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportement des teintes et des tolérances dans AKS

Lorsque vous mettez à niveau un pool de nœuds dans AKS, les teintes et les tolérances suivent un modèle défini lorsqu’elles sont appliquées à de nouveaux nœuds :

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Clusters par défaut qui utilisent des groupes de machines virtuelles identiques
Vous pouvez [appliquer une teinte à un pool de nœuds][taint-node-pool] à partir de l’API AKS afin que les nœuds sur lesquels un scale-out vient d’être effectué reçoivent des teintes de nœud spécifiés par l’API.

Supposons que :
1. Vous commencez avec un cluster à deux nœuds : *node1* et *node2*. 
1. Vous mettez à niveau le pool de nœuds.
1. Deux nœuds supplémentaires sont créés : *node3* et *node4*. 
1. Les teintes sont transmises respectivement.
1. Les *node1* et *node2* originaux sont supprimés.

#### <a name="clusters-without-vm-scale-set-support"></a>Clusters sans prise en charge des groupes de machines virtuelles identiques

Là encore, supposons que :
1. Vous avez un cluster à deux nœuds : *node1* et *node2*. 
1. Vous mettez à niveau le pool de nœuds.
1. Un nœud supplémentaire est créé : *node3*.
1. Les teintes de *node1* sont appliquées à *node3*.
1. *node1* est supprimé.
1. Un nouveau *node1* est créé pour remplacer le *node1* d’origine.
1. Les teintes de *node2* sont appliquées au nouveau *node1*. 
1. *node2* est supprimé.

En substance, *node1* devient *node3* et *node2* devient le nouveau *node1*.

Lorsque vous faites évoluer un pool de nœuds dans AKS, les teintes et les tolérances ne sont pas transmises par défaut.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Contrôler la planification des pods à l’aide de sélecteurs de nœud et de l’affinité de nœud

> **Conseils sur les bonnes pratiques** 
> 
> Contrôlez la planification des pods sur les nœuds à l’aide de sélecteurs de nœuds, de l’affinité entre nœuds ou de l’affinité entre pods. Ces paramètres permettent au planificateur Kubernetes d’isoler logiquement les charges de travail, notamment par matériel dans le nœud.

Les teintes et les tolérances isolent logiquement les ressources avec une coupure dure. Si le pod ne tolère pas la teinte d’un nœud, il n’est pas planifié sur ce nœud. 

Vous pouvez également utiliser des sélecteurs de nœuds. Par exemple, vous étiquetez les nœuds pour indiquer un stockage SSD attaché localement ou une grande quantité de mémoire, puis vous définissez un sélecteur de nœuds dans la spécification du pod. Kubernetes planifie ces pods sur un nœud correspondant. 

Contrairement aux tolérances, les pods sans sélecteur de nœuds correspondant peuvent toujours être planifiés sur des nœuds étiquetés. Ce comportement autorise la consommation des ressources inutilisées sur les nœuds, mais donne la priorité aux pods qui définissent le sélecteur de nœuds correspondant.

Examinons un exemple de nœuds avec une grande quantité de mémoire. Ces nœuds donnent la priorité aux pods qui demandent une grande quantité de mémoire. Pour s’assurer que les ressources ne restent pas inutilisées, d’autres pods sont également autorisés à s’exécuter.

```console
kubectl label node aks-nodepool1 hardware=highmem
```

Une spécification de pod ajoute ensuite la propriété `nodeSelector` pour définir un sélecteur de nœud qui correspond à l’étiquette définie sur un nœud :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Quand vous utilisez ces options du planificateur, collaborez avec les propriétaires et développeurs d’applications pour leur permettre de définir correctement leurs spécifications de pod.

Pour plus d’informations sur l’utilisation de sélecteurs de nœud, consultez [Affectation de pods à des nœuds][k8s-node-selector].

### <a name="node-affinity"></a>Affinité de nœud

Un sélecteur de nœuds est une solution de base permettant d’attribuer des pods à un nœud donné. L’*affinité de nœud* offre plus de flexibilité, vous permettant de définir ce qui se passe si le pod ne peut pas être mis en correspondance avec un nœud. Vous pouvez : 
* *exiger* que le planificateur Kubernetes mette en correspondance un pod avec un hôte étiqueté ; Ou,
* *préférer* une correspondance, mais autoriser la planification du pod sur un autre hôte si aucune correspondance n’est disponible.

L’exemple suivant définit l’affinité de nœud avec la valeur *requiredDuringSchedulingIgnoredDuringExecution*. Selon cette affinité, la planification Kubernetes doit utiliser un nœud avec une étiquette correspondante. Si aucun nœud n’est disponible, le pod doit attendre pour que planification puisse se poursuivre. Pour autoriser la planification du pod sur un autre nœud, vous pouvez définir la valeur sur ***preferred** DuringSchedulingIgnoreDuringExecution* :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

La partie *IgnoredDuringExecution* du paramètre indique que le pod ne doit pas être supprimé du nœud si ses étiquettes viennent à changer. Le planificateur Kubernetes n’utilise les étiquettes de nœud mises à jour que pour les nouveaux pods en cours de planification, pas pour ceux déjà planifiés sur les nœuds.

Pour plus d’informations, consultez [Affinité et anti-affinité][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Affinité et anti-affinité entre pods

Une dernière approche, employée par le planificateur Kubernetes pour isoler logiquement des charges de travail, consiste à utiliser l’affinité ou l’anti-affinité entre pods. Ces paramètres définissent si *oui* ou *non* des pods peuvent être planifiés sur un nœud ayant un pod correspondant. Par défaut, le planificateur Kubernetes essaie de planifier plusieurs pods dans un jeu de réplicas sur les nœuds. Vous pouvez définir des règles plus spécifiques autour de ce comportement.

Par exemple, vous avez une application web qui utilise également Azure Cache pour Redis. 
1. Vous utilisez des règles d’anti-affinité de pods pour demander au planificateur Kubernetes de distribuer les réplicas entre les nœuds. 
1. Vous utilisez des règles d’affinité pour vous assurer que chaque composant de l’application web est planifié sur le même hôte que le cache correspondant. 

La distribution des pods sur les nœuds est similaire à celle de l’exemple suivant :

| **Nœud 1** | **Nœud 2** | **Nœud 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

L’affinité et l’anti-affinité entre pods permettent un déploiement plus complexe que les sélecteurs de nœuds ou l’affinité des nœuds. Grâce à ce déploiement, vous isolez logiquement les ressources et contrôlez la manière dont Kubernetes planifie les pods sur les nœuds. 

Pour obtenir un exemple complet de cette application web avec un exemple Azure Cache pour Redis, consultez [Colocaliser des pods sur le même nœud][k8s-pod-affinity].

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons traité des fonctionnalités avancées du planificateur Kubernetes. Pour plus d’informations sur les opérations liées aux clusters dans AKS, consultez les bonnes pratiques suivantes :

* [Multilocation et isolation de cluster][aks-best-practices-scheduler]
* [Fonctionnalités de base du planificateur Kubernetes][aks-best-practices-scheduler]
* [Authentification et autorisation][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
