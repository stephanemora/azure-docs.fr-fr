---
title: Comprenez le fonctionnement des alertes de métrique dans Azure Monitor.
description: Obtenez un aperçu des actions possibles avec les alertes de métriques et de leur fonctionnement dans Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 586ced5b239b77dd9ae596a754613a66cee371a9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405918"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Comprendre le fonctionnement des alertes de métrique dans Azure Monitor

Les alertes de métrique dans Azure Monitor fonctionnent en complément des métriques multidimensionnelles. Il peut s’agir de métriques de la plateforme, de [métriques personnalisés](metrics-custom-overview.md), de [journaux courants de Log Analytics convertis en métriques](monitoring-metric-alerts-logs.md) ou de métriques standard d’Application Insights. Les alertes de métrique vérifient à intervalles réguliers que les conditions d’une ou de plusieurs séries chronologiques de métriques sont remplies et vous avertissent quand c’est le cas. Les alertes de métrique sont avec état ; autrement dit, elles envoient uniquement des notifications lorsque l’état change.

## <a name="how-do-metric-alerts-work"></a>Fonctionnement des alertes de métrique

Vous pouvez définir une règle d’alerte de métrique en spécifiant une ressource cible à surveiller, un nom de métrique et la condition (un opérateur et un seuil), ainsi qu’un groupe d’actions à déclencher lorsque la règle d’alerte se déclenche.
Supposons que vous avez créé une règle d’alerte métrique simple comme suit :

- Ressource cible (ressource Azure à surveiller) : myVM
- Métrique : Pourcentage UC
- Agrégation du temps (statistique exécutée sur des valeurs de mesures brutes ; les agrégations de temps prises en charge sont Min, Max, Moy, Total) : Moyenne
- Période (fenêtre de vérification des valeurs de métrique) : 5 dernières minutes
- Fréquence (fréquence à laquelle l’alerte de métrique vérifie si les conditions sont remplies) : 1 minute
- Opérateur : Supérieur à
- Seuil : 70

À partir de la création de la règle d’alerte, l’analyse s’exécute chaque minute. Elle examine les valeurs de métrique des 5 dernières minutes et vérifie si la moyenne de ces valeurs est supérieure à 70. Si la condition est remplie (autrement dit, le pourcentage d’UC moyen pour les 5 dernières minutes est supérieur à 70), la règle d’alerte déclenche une notification d’activation. Si vous avez configuré un e-mail ou une action de webhook dans le groupe d’actions associé à la règle d’alerte, vous recevrez une notification sur les deux.

Cette instance particulière de l’activation de règle d’alerte peut également être affichée dans le portail Azure, dans le panneau Toutes les alertes.

Par exemple, si l’utilisation de « myVM » demeure au-dessus du seuil lors les vérifications suivantes, la règle d’alerte ne sera pas déclenchée à nouveau tant que la condition ne sera pas résolue.

Après quelque temps, si l’utilisation de « myVM » revient à la normale (au-dessous du seuil spécifié), la règle d’alerte surveille l’état deux fois de plus, puis envoie une notification de résolution. La règle d’alerte envoie un message de résolution/désactivation lorsque la condition d’alerte n’est pas remplie pendant trois périodes consécutives pour réduire le bruit en cas de flottement de conditions.

Comme la notification de résolution est envoyée par le biais de webhooks ou de l’e-mail, l’état de l’instance d’alerte (état de l’analyse) dans le portail Azure est également défini comme résolu.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Surveillance à l’échelle à l’aide d’alertes de métrique dans Azure Monitor

### <a name="using-dimensions"></a>Utilisation de dimensions

Les alertes de métrique dans Azure Monitor prennent également en charge la surveillance de plusieurs combinaisons de valeurs de dimension avec une seule règle. Essayons de comprendre pourquoi vous pouvez utiliser plusieurs combinaisons de dimension à l’aide d’un exemple.

Supposons que vous avez un plan App Service pour votre site web. Vous souhaitez surveiller l’utilisation du processeur sur plusieurs instances de votre site web/application. Vous pouvez le faire à l’aide d’une règle d’alerte de métrique, comme suit :

- Ressource cible : myAppServicePlan
- Métrique : Pourcentage UC
- Dimensions
  - Instance = InstanceName1, InstanceName2
- Agrégation du temps : Moyenne
- Période : 5 dernières minutes
- Fréquence : 1 minute
- Opérateur : GreaterThan
- Seuil : 70

Comme précédemment, cette règle contrôle si l’utilisation moyenne du processeur pendant les 5 dernières minutes est supérieure à 70 %. Toutefois, avec la même règle, vous pouvez surveiller deux instances de votre site web. Chaque instance est surveillée individuellement, et vous recevez des notifications séparées.

Admettons que vous avez une application web soumise à une très forte demande, et vous devez ajouter d’autres instances. Cette règle ne surveille toujours que deux instances. Toutefois, vous pouvez créer une règle comme suit :

- Ressource cible : myAppServicePlan
- Métrique : Pourcentage UC
- Dimensions
  - Instance = *
- Agrégation du temps : Moyenne
- Période : 5 dernières minutes
- Fréquence : 1 minute
- Opérateur : GreaterThan
- Seuil : 70

Cette règle surveille automatiquement toutes les valeurs pour de l’instance, c’est-à-dire que vous pouvez surveiller les nouvelles instances dès leur ajout sans avoir à modifier votre règle d’alerte de métrique.

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Supervision de plusieurs ressources à l’aide d’alertes de métrique

Comme vous l’avez vu dans la section précédente, il est possible d’avoir une seule règle d’alerte de métrique unique qui surveille chaque combinaison de dimensions (par exemple, une série chronologique de métriques). Toutefois, vous étiez auparavant toujours contraint de n’effectuer l’opération que sur une seule ressource à la fois. Azure Monitor prend également en charge la supervision de plusieurs ressources avec une seule règle d’alerte de métrique. Cette fonctionnalité est actuellement disponible en préversion et n’est prise en charge que sur les machines virtuelles. En outre, une alerte de métrique unique ne peut superviser les ressources que dans une seule région Azure.

Vous disposez de trois méthodes pour spécifier l’étendue de la supervision par une alerte de métrique unique :

- sous la forme d’une liste de machines virtuelles dans une seule région Azure d’un abonnement ;
- pour toutes les machines virtuelles (dans une seule région Azure) dans un ou plusieurs groupes de ressources d’un abonnement ;
- pour toutes les machines virtuelles (dans une seule région Azure) d’un abonnement.

La création de règles d’alerte de métrique surveillant plusieurs ressources n’est pas actuellement prise en charge via le portail Azure. Vous pouvez créer ces règles par le biais des [modèles Azure Resource Manager](monitoring-create-metric-alerts-with-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources). Vous recevrez des notifications individuelles pour chaque machine virtuelle. 

## <a name="typical-latency"></a>Latence classique

Pour les alertes de métrique, vous êtes généralement informé en moins de 5 minutes si vous définissez la fréquence de règle d’alerte sur 1 minute. En cas de forte charge sur les systèmes de notification, vous pouvez observer une latence plus longue.

## <a name="supported-resource-types-for-metric-alerts"></a>Types de ressources pris en charge pour les alertes de métrique

Vous trouverez la liste complète des types de ressources pris en charge dans cet [article](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir comment créer, afficher et gérer des alertes de métrique dans Azure](alert-metric.md)
- [Découvrir comment déployer des alertes de métrique à l’aide de modèles Azure Resource Manager](monitoring-create-metric-alerts-with-templates.md)
- [En savoir plus sur les groupes d’actions](monitoring-action-groups.md)
