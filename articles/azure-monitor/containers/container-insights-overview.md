---
title: Vue d’ensemble de Container Insights | Microsoft Docs
description: Cet article décrit Container Insights qui surveille la solution AKS Container Insights et la valeur qu’elle offre en surveillant l’intégrité de vos clusters AKS et de Container Instances dans Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 1055e2228c6625ae24e6bf388cf297e3e3363666
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723382"
---
# <a name="container-insights-overview"></a>Vue d’ensemble de Container Insights

Container Insights est une fonctionnalité conçue pour surveiller les performances des charges de travail de conteneur déployées sur :

- des clusters Kubernetes managés hébergés sur [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- des clusters Kubernetes automanagés hébergés sur Azure à l’aide du [moteur AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- des clusters Kubernetes automanagés hébergés sur [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) ou localement
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Kubernetes avec Azure Arc activé](../../azure-arc/kubernetes/overview.md) (préversion)

Container Insights prend en charge les clusters exécutant le système d’exploitation Linux et Windows Server 2019. Les runtimes de conteneur qu’il prend en charge sont Docker, Moby et tout runtime compatible CRI, tel que élément CRI-O et ContainerD.

La surveillance de vos conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle, avec plusieurs applications.

Container Insights vous permet de visualiser les performances en collectant des métriques sur la mémoire et le processeur à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. Les journaux d’activité de conteneur sont aussi collectés.  Une fois que vous avez activé la supervision des clusters Kubernetes, les métriques et les journaux d’activité sont automatiquement collectés à l’aide d’une version conteneurisée de l’agent Log Analytics pour Linux. Les métriques sont rédigées dans la banque de métriques et les données de journaux dans la banque de journaux associée à votre espace de travail [Log Analytics](../logs/log-query-overview.md).

![Architecture de Container Insights](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>Qu’apporte Container Insights ?

Container Insights offre une expérience de surveillance complète à l’aide des différentes fonctionnalités d’Azure Monitor. Ces fonctionnalités vous permettent d’avoir une meilleure visibilité des performances et de l’intégrité de votre cluster Kubernetes Linux ou Windows Server 2019 ainsi que des charges de travail des conteneurs. Avec Container Insights, vous pouvez :

* Identifier les conteneurs AKS en cours d’exécution sur le nœud, ainsi que leur utilisation moyenne du processeur et de la mémoire. Cette information peut vous aider à identifier les goulots d’étranglement des ressources.
* Identifier l’utilisation du processeur et de la mémoire des groupes de conteneurs et de leurs conteneurs hébergés dans Azure Container Instances.
* Identifier l’emplacement du conteneur dans un contrôleur ou un pod. Cette information peut vous permettre de voir les performances globales du contrôleur ou du pod.
* Voir la quantité de ressources utilisée par les charges de travail qui sont exécutées sur l’hôte et qui ne sont pas liées aux processus standard nécessaires à la prise en charge du pod
* Comprendre le comportement du cluster lorsqu’il subit des charges moyennes et très importantes. Cette information peut vous aider à identifier les besoins en capacité et à déterminer la charge maximale que le cluster peut supporter.
* Configurer des alertes pour vous avertir de manière proactive ou les enregistrer lorsque l’utilisation de l’UC et de la mémoire sur des nœuds ou des conteneurs dépasse vos seuils, ou encore lorsqu’un changement d’état d’intégrité se produit dans le cluster au niveau de l’infrastructure ou du cumul d’intégrité des nœuds.
* Bénéficier d’une intégration à [Prometheus](https://prometheus.io/docs/introduction/overview/) pour afficher les métriques d’application et de charge de travail qu’il collecte à partir des nœuds et Kubernetes à l’aide de [requêtes](container-insights-log-search.md) pour créer des alertes personnalisées, des tableaux de bord et une analyse détaillée des performances.
* Surveiller les charges de travail de conteneur [déployées sur le moteur AKS](https://github.com/Azure/aks-engine) local et le [moteur AKS sur Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
* Surveiller les charges de travail de conteneur [déployées sur Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >La prise en charge d’Azure Red Hat OpenShift est actuellement une fonctionnalité en préversion publique.
    >

* Surveiller les charges de travail de conteneur [déployées sur Kubernetes avec Azure Arc activé (préversion)](../../azure-arc/kubernetes/overview.md).

Les principales différences entre la supervision d’un cluster Windows Server et celle d’un cluster Linux sont les suivantes :

- Windows ne dispose pas d’une métrique Mémoire RSS et celle-ci n’est donc pas disponible pour le nœud et les conteneurs Windows. La métrique [Plage de travail](/windows/win32/memory/working-set) est disponible.
- Les informations de capacité de stockage des disques ne sont pas disponibles pour les nœuds Windows.
- Seuls les environnements de pod sont surveillés, pas les environnements Docker.
- Avec la préversion, un maximum de 30 conteneurs Windows Server sont pris en charge. Cette limitation ne s’applique pas aux conteneurs Linux.

Regardez la vidéo suivante qui fournit une présentation approfondie de niveau intermédiaire pour vous apprendre à surveiller votre cluster AKS avec Container Insights.

> [!VIDEO https://youtu.be/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>Comment accéder à cette fonctionnalité ?

Vous pouvez accéder à Container Insights de deux manières : à partir d’Azure Monitor ou directement à partir du cluster AKS sélectionné. À partir d’Azure Monitor, vous disposez d’une perspective globale de tous les conteneurs déployés, tant surveillés que non surveillés, qui vous permet de rechercher et de filtrer sur vos abonnements et groupes de ressources, puis d’explorer Container Insights à partir du conteneur sélectionné.  Sinon, vous pouvez accéder directement à la fonctionnalité à partir d’un conteneur AKS sélectionné dans la page AKS.

![Vue d’ensemble des méthodes d’accès à Container Insights](./media/container-insights-overview/azmon-containers-experience.png)

Si vous souhaitez surveiller et gérer vos hôtes de conteneur Docker et Windows exécutés en dehors d’AKS pour afficher la configuration, un audit et l’utilisation des ressources, consultez la [solution de supervision des conteneurs](./containers.md).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster Kubernetes, consultez [Comment activer Container Insights](container-insights-onboard.md) afin de comprendre les exigences et les méthodes disponibles pour activer la surveillance.
