---
title: Configuration de moniteurs d’intégrité Azure Monitor pour conteneurs | Microsoft Docs
description: Cet article fournit des informations sur la configuration détaillée des moniteurs d’intégrité dans Azure Monitor pour conteneurs.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: d2d602d767fa6a39b7f72650c426c90be210a6ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405036"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Guide de configuration des moniteurs d’intégrité Azure Monitor pour conteneurs

Les moniteurs sont l’élément principal permettant de mesurer l’intégrité et de détecter les erreurs dans Azure Monitor pour conteneurs. Cet article vous aide à comprendre les concepts de mesure de l’intégrité et les éléments qui composent le modèle d’intégrité pour superviser et signaler l’intégrité de votre cluster Kubernetes à l’aide de la fonctionnalité (d’évaluation) [Intégrité](container-insights-health.md).

>[!NOTE]
>La fonctionnalité Intégrité est actuellement une fonctionnalité d’évaluation publique.
>

## <a name="monitors"></a>Analyses

Un moniteur mesure l’intégrité de certains aspects d’un objet managé. Les moniteurs ont deux ou trois états d’intégrité. Un moniteur sera dans un seul de ses états potentiels à un moment donné. Lorsqu’un moniteur est chargé par l’agent mis en conteneur, il est initialisé à un état sain. L’état change uniquement si les conditions spécifiées pour un autre état sont détectées.

L’intégrité globale d’un objet particulier est déterminée à partir de l’intégrité de chacun de ses moniteurs. Cette hiérarchie est illustrée dans le volet Hiérarchie d’intégrité d’Azure Monitor pour conteneurs. La stratégie pour le regroupement de l’intégrité fait partie de la configuration des moniteurs agrégés.

## <a name="types-of-monitors"></a>Types de moniteurs

|Moniteur | Description | 
|--------|-------------|
| Moniteur d’unités |Un moniteur d’unités mesure certains aspects d’une ressource ou d’une application. Il peut s’agir de la vérification d’un compteur de performances pour déterminer le niveau de performance de la ressource ou sa disponibilité. |
|Moniteur agrégé | Les moniteurs agrégés regroupent plusieurs moniteurs pour fournir un état d’intégrité agrégé d’intégrité unique. Les moniteurs d’unités sont généralement configurées sous un moniteur agrégé particulier. Par exemple, un moniteur agrégé de nœuds regroupe l’état de l’utilisation de l’UC du nœud, l’utilisation de la mémoire et l’état du nœud.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Stratégie de cumul d’intégrité des moniteurs agrégés

Chaque moniteur agrégé définit une stratégie de cumul d’intégrité, qui est la logique utilisée pour déterminer l’intégrité du moniteur agrégé en fonction de l’intégrité des moniteurs qui se trouvent sous lui. Les stratégies de cumul d’intégrité possibles pour un moniteur agrégé sont les suivantes :

#### <a name="worst-state-policy"></a>Stratégie de pire état

L’état du moniteur agrégé correspond à celui du moniteur enfant ayant le pire état d’intégrité. Il s’agit de la stratégie la plus courante utilisée par les moniteurs agrégés.

![Exemple de pire état du cumul des moniteurs agrégés](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Stratégie de pourcentage

L’objet source correspond au pire état du membre unique d’un pourcentage spécifié d’objets cibles dans le meilleur état. Cette stratégie est utilisée quand un certain pourcentage d’objets cibles doit être sain pour que l’objet cible soit considéré comme sain. La stratégie de pourcentage trie les moniteurs dans l’ordre décroissant de gravité de l’état, et l’état du moniteur agrégé est calculé comme étant le pire état de N % (N est dicté par le paramètre de configuration *StateThresholdPercentage*).

Par exemple, supposons qu’il existe cinq instances de conteneur d’une image conteneur, et que leurs états individuels sont **Critique**, **Avertissement**, **Sain**, **Sain** et **Sain**.  L’état du moniteur d’utilisation de l’UC du conteneur est **Critique**, car le pire état de 90 % des conteneurs est **Critique** lorsqu’ils sont triés par ordre de gravité décroissant.

## <a name="understand-the-monitoring-configuration"></a>Comprendre la configuration de l’analyse

Azure Monitor pour conteneurs comprend un certain nombre de scénarios d’analyse clés configurés comme suit.

### <a name="unit-monitors"></a>Moniteurs d’unités

|**Nom du moniteur** | Type de moniteur | **Description** | **Paramètre** | **Valeur** |
|-----------------|--------------|-----------------|---------------|-----------|
|Utilisation de mémoire du nœud |Moniteur d’unités |Ce moniteur évalue l’utilisation de la mémoire d’un nœud toutes les minutes, à l’aide des données signalées par cadvisor. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Utilisation de l’UC du nœud |Moniteur d’unités |Ce moniteur vérifie l’utilisation de l’UC du nœud toutes les minutes, à l’aide des données signalées par cadvisor. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|État du nœud |Moniteur d’unités |Ce moniteur vérifie les conditions de nœud signalées par Kubernetes.<br> Actuellement, les conditions de nœud suivantes sont vérifiées : sollicitation du disque, sollicitation de la mémoire, sollicitation PID, espace disque insuffisant, réseau non disponible, état prêt pour le nœud.<br> Parmi les conditions ci-dessus, si *Espace disque insuffisant* ou *Réseau non disponible* est **true**, le moniteur passe à l’état **Critique**.<br> Si d’autres conditions sont égales à **true**, à l’exception de l’état **Prêt**, le moniteur passe à l’état **Avertissement**. | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Utilisation de mémoire du conteneur |Moniteur d’unités |Ce moniteur signale l’état d’intégrité combiné de l’utilisation de la mémoire (RSS) des instances du conteneur.<br> Il effectue une comparaison simple qui compare chaque échantillon à un seuil unique et spécifié par le paramètre de configuration **ConsecutiveSamplesForStateTransition**.<br> Son état est calculé comme étant le pire état de 90 % des instances de conteneur (StateThresholdPercentage), trié dans l’ordre décroissant de gravité de l’état d’intégrité du conteneur (autrement dit, Critique, Avertissement, Sain).<br> Si aucun enregistrement n’est reçu d’une instance de conteneur, l’état d’intégrité de l’instance de conteneur est signalé comme **Inconnu** et a une priorité plus élevée dans l’ordre de tri par rapport à l’état **Critique**.<br> L’état de chaque instance individuelle de conteneur est calculé à l’aide des seuils spécifiés dans la configuration. Si l’utilisation est supérieure au seuil critique (90 %), l’instance est alors dans un état **Critique**. Si elle est inférieure au seuil critique (90 %), mais supérieure au seuil d’avertissement (80 %), l’instance est alors dans un état **Avertissement**. Dans le cas contraire, elle est dans un état **Sain**. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Utilisation de l’UC du conteneur |Moniteur d’unités |Ce moniteur signale l’état d’intégrité combiné de l’utilisation de l’UC des instances du conteneur.<br> Il effectue une comparaison simple qui compare chaque échantillon à un seuil unique et spécifié par le paramètre de configuration **ConsecutiveSamplesForStateTransition**.<br> Son état est calculé comme étant le pire état de 90 % des instances de conteneur (StateThresholdPercentage), trié dans l’ordre décroissant de gravité de l’état d’intégrité du conteneur (autrement dit, Critique, Avertissement, Sain).<br> Si aucun enregistrement n’est reçu d’une instance de conteneur, l’état d’intégrité de l’instance de conteneur est signalé comme **Inconnu** et a une priorité plus élevée dans l’ordre de tri par rapport à l’état **Critique**.<br> L’état de chaque instance individuelle de conteneur est calculé à l’aide des seuils spécifiés dans la configuration. Si l’utilisation est supérieure au seuil critique (90 %), l’instance est alors dans un état **Critique**. Si elle est inférieure au seuil critique (90 %), mais supérieure au seuil d’avertissement (80 %), l’instance est alors dans un état **Avertissement**. Dans le cas contraire, elle est dans un état **Sain**. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Pods de charge de travail système prêts |Moniteur d’unités |Ce moniteur signale l’état en fonction du pourcentage de pods à l’état prêt pour une charge de travail donnée. Son état est défini sur **Critique** si moins de 100 % des pods sont dans un état **Sain** |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|État de l’API Kube |Moniteur d’unités |Ce moniteur signale l’état du service d’API Kube. Le moniteur est dans un état critique si le point de terminaison de l’API Kube n’est pas disponible. Pour ce moniteur particulier, l’état est déterminé en effectuant une requête sur le point de terminaison « nœuds » pour le serveur kube-api. Tout code de réponse autre que OK passe le moniteur à un état **Critique**. | Aucune propriété de configuration |||

### <a name="aggregate-monitors"></a>Moniteurs agrégés

|**Nom du moniteur** | **Description** | **Algorithme** |
|-----------------|-----------------|---------------|
|Nœud |Ce moniteur est un agrégat de tous les moniteurs de nœud. Il correspond à l’état du moniteur enfant ayant le pire état d’intégrité :<br> Utilisation de l’UC du nœud<br> Utilisation de la mémoire du nœud<br> État du nœud | Le pire de|
|Pool de nœuds |Ce moniteur signale l’état d’intégrité combiné de tous les nœuds du pool de nœuds *agentpool*. Il s’agit d’un moniteur à trois états, dont l’état est basé sur le pire état de 80 % des nœuds du pool de nœuds, triés dans l’ordre décroissant de gravité des états de nœud (c’est-à-dire Critique, Avertissement, Sain).|Pourcentage |
|Nœuds (parent du pool de nœuds) |Il s’agit d’un moniteur agrégé de tous les pools de nœuds. Son état est basé sur le pire état de ses moniteurs enfants (c’est-à-dire, les pools de nœuds présents dans le cluster). |Le pire de |
|Cluster (parent des nœuds/<br> infrastructure Kubernetes) |Il s’agit du moniteur parent qui correspond à l’état du moniteur enfant ayant le pire état d’intégrité, c’est-à-dire l’infrastructure kubernetes et les nœuds. |Le pire de |
|l’infrastructure Kubernetes |Ce moniteur signale l’état d’intégrité combiné des composants de l’infrastructure managée du cluster. Son état est calculé comme étant le « pire de » ses états de moniteurs enfants, c.-à-d. les charges de travail kube-system et l’état du serveur d’API. |Le pire de|
|la charge de travail système |Ce moniteur signale l’état d’intégrité d’une charge de travail kube-system. Ce moniteur correspond à l’état du moniteur enfant ayant le pire état d’intégrité, à savoir **Pods à l’état prêt** (moniteur et les conteneurs de la charge de travail). |Le pire de |
|Conteneur |Ce moniteur signale l’état d’intégrité global d’un conteneur dans une charge de travail donnée. Ce moniteur correspond à l’état du moniteur enfant ayant le pire état d’intégrité. Il s’agit des moniteurs **Utilisation de l’UC** et **Utilisation de la mémoire**. |Le pire de |

## <a name="next-steps"></a>Étapes suivantes

Consultez [Superviser l’intégrité des clusters](container-insights-health.md) pour en savoir plus sur l’affichage de l’état d’intégrité de votre cluster Kubernetes.
