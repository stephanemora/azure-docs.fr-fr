---
title: Concepts – Génie logiciel durable dans Azure Kubernetes Service (AKS)
description: En savoir plus sur le génie logiciel durable dans Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011489"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Principes du génie logiciel durable dans Azure Kubernetes service (AKS)

Les principes du génie logiciel durable sont un ensemble de compétences visant à vous aider à définir, créer et exécuter des applications durables. L’objectif global est de réduire l’empreinte carbone de tous les aspects de votre application. La page [Principes du génie logiciel durable][principles-sse] présente une vue d’ensemble de ces principes.

Le génie logiciel durable est un déplacement de priorités et de focalisations. Dans de nombreux cas, la façon dont la plupart des logiciels sont conçus et exécutés met en évidence des performances rapides et une faible latence. En attendant, le génie logiciel durable vise à réduire autant que possible les émissions de carbone. Vous devez :

* L’application de principes de génie logiciel durable peut offrir des performances plus rapides ou une latence plus faible. Par exemple, en réduisant le nombre total de déplacements sur le réseau. 
* La réduction des émissions de carbone peut entraîner un ralentissement des performances ou une latence accrue. Par exemple, en retardant des charges de travail de faible priorité. 

Avant l’application de principes de génie logiciel durable à votre application, vérifiez les priorités, les besoins et les compromis de votre application.

## <a name="measure-and-optimize"></a>Mesurer et optimiser

Pour réduire l’empreinte carbone de vos clusters AKS, vous devez comprendre de quelle façon les ressources de votre cluster sont utilisées. [Azure Monitor][azure-monitor] fournit des détails sur l’utilisation des ressources de votre cluster, telles que l’utilisation de la mémoire et du processeur. Ces données peuvent vous aider lors de prises de décisions pour réduire l’empreinte carbone de votre cluster et observer l’effet de vos modifications. 

Vous pouvez également installer [Microsoft Sustainability Calculator][sustainability-calculator] pour connaître l’empreinte carbone de toutes vos ressources Azure.

## <a name="increase-resource-utilization"></a>Augmenter l’utilisation des ressources

L’une des méthodes permettant de réduire votre empreinte carbone consiste à réduire votre délai d’inactivité. La réduction de la durée d’inactivité implique une augmentation de l’utilisation de vos ressources de calcul. Par exemple :
1. Vous aviez quatre nœuds dans votre cluster, chacun s’exécutant à une capacité de 50 %. Ainsi, les quatre nœuds ont une capacité inutilisée de 50 % demeurant inactive. 
1. Vous avez réduit votre cluster à trois nœuds, chacun s’exécutant à 67 % de capacité avec la même charge de travail. Vous auriez pu réduire correctement la capacité inutilisée à 33 % sur chaque nœud et augmenter votre taux d’utilisation.

> [!IMPORTANT]
> Lorsque vous envisagez d’apporter des modifications aux ressources de votre cluster, vérifiez que vos [pools de systèmes][system-pools] disposent de suffisamment de ressources pour maintenir la stabilité des composants du votre système central de cluster. Ne réduisez **jamais** les ressources de votre cluster au point qu’il en devienne instable.

Après avoir examiné l’utilisation de votre cluster, envisagez d’utiliser les fonctionnalités offertes par [les pools multinœuds][multiple-node-pools] : 

* Dimensionnement de nœuds

    Utilisez le [dimensionnement des nœuds][node-sizing] pour définir des pools de nœuds avec des profils de processeur et de mémoire spécifiques, afin de pouvoir adapter vos nœuds aux besoins de votre charge de travail. En modifiant la dimension de vos nœuds selon les besoins de votre charge de travail, vous pouvez exécuter quelques nœuds à un taux d’utilisation plus élevé. 

* Mise à l’échelle du cluster

    Configurez [la mise à l’échelle][scale] de votre cluster. Utilisez [l’autoscaler de pods horizontaux][scale-horizontal] et [l’autoscaler de clusters][scale-auto] pour mettre votre cluster à l’échelle automatiquement en fonction de votre configuration. Le contrôle de la mise à l’échelle de votre cluster peut vous aider à maintenir tous vos nœuds en exécution à un niveau d’utilisation élevé tout en étant synchrone avec la charge de travail de votre cluster. 

* Pools spots

    Dans les cas où une charge de travail tolère des perturbations ou des arrêts soudains, vous pouvez utiliser des [pools spots][spot-pools]. Les pools spots tirent parti de la capacité inactive dans Azure. Par exemple, les pools spots peuvent fonctionner correctement pour les programmes de traitement par lots ou les environnements de développement.

> [!NOTE]
>L’augmentation de l’utilisation peut également réduire le nombre de nœuds excédentaires, ce qui réduit l’énergie consommée par les [réservations de ressources sur chaque nœud][resource-reservations].

Enfin, examinez les *requêtes* et les *limites* de processeur et de mémoire dans les manifestes Kubernetes de vos applications. 
* À mesure que vous réduisez ces valeurs de mémoire et de processeur, le cluster dispose de davantage de mémoire et de processeur pour exécuter d’autres charges de travail. 
* À mesure que vous exécutez plus de charges de travail avec moins de processeur et de mémoire, l’allocation de votre cluster devient plus dense, ce qui augmente votre utilisation. 

Lorsque vous réduisez les valeurs de processeur et de mémoire de vos applications, le comportement de vos applications peut se dégrader ou devenir instable si vous les définissez sur une valeur trop faible. Avant de modifier les *requêtes* et les *limites* du processeur et de la mémoire, exécutez des tests de benchmarking pour savoir si ces valeurs sont définies de manière appropriée. Ne réduisez jamais ces valeurs au point d’instabilité de l’application.

## <a name="reduce-network-travel"></a>Réduire les déplacements sur le réseau

En réduisant la distance entre les requêtes et les réponses vers et depuis votre cluster, vous pouvez réduire les émissions de carbone et la consommation d’électricité par les appareils réseaux. Après avoir examiné votre trafic, envisagez de créer des clusters [dans des régions][regions] plus proches de la source de votre trafic. Vous pouvez également utiliser [Azure Traffic Manager][azure-traffic-manager] pour vous aider à acheminer le trafic vers le cluster le plus proche et vers les [groupes de placement de proximité][proiximity-placement-groups], ainsi que pour réduire la distance entre les ressources Azure.

> [!IMPORTANT]
> Lorsque vous envisagez d’apporter des modifications à la mise en réseau de votre cluster, ne réduisez jamais les déplacements sur le réseau au détriment de la charge de travail. Par exemple, l’utilisation de [zones de disponibilité][availability-zones] entraîne davantage de déplacements réseau sur votre cluster, mais l’utilisation de cette fonctionnalité peut être nécessaire pour gérer les exigences de la charge de travail.

## <a name="demand-shaping"></a>Façonner la requête

Dans la mesure du possible, envisagez de déplacer la demande de ressources de votre cluster vers des heures ou des régions où vous pouvez utiliser une capacité excédentaire. Par exemple, prenez en compte les points suivants :
* Modification de l’heure et de la région pour l’exécution d’un programme de traitement par lots.
* Utilisation de [pools spots][spot-pools]. 
* Refactorisation de votre application afin d’utiliser une file d’attente pour différer l’exécution des charges de travail qui n’ont pas besoin d’un traitement immédiat.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les fonctionnalités ’AKS mentionnées dans cet article :

* [Pools multinœuds][multiple-node-pools]
* [Dimensionnement de nœuds][node-sizing]
* [Mise à l’échelle d’un cluster][scale]
* [Autoscaler de pods horizontaux][scale-horizontal]
* [Autoscaler de cluster][scale-auto]
* [Pools spots][spot-pools]
* [Pools de système][system-pools]
* [Réservations de ressources][resource-reservations]
* [Groupes de placement de proximité][proiximity-placement-groups]
* [Zones de disponibilité][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/