---
title: Comprenez le fonctionnement des alertes de métrique dans Azure Monitor.
description: Obtenez un aperçu des actions possibles avec les alertes de métriques et de leur fonctionnement dans Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 9689854d9a28debbfbcf908391806fffac6a2006
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450173"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Comprendre le fonctionnement des alertes de métrique dans Azure Monitor

Les alertes de métrique dans Azure Monitor fonctionnent en complément des métriques multidimensionnelles. Il peut s’agir de métriques de la plateforme, de [métriques personnalisés](../../azure-monitor/platform/metrics-custom-overview.md), de [journaux courants de Log Analytics convertis en métriques](../../azure-monitor/platform/alerts-metric-logs.md) ou de métriques standard d’Application Insights. Les alertes de métrique vérifient à intervalles réguliers que les conditions d’une ou de plusieurs séries chronologiques de métriques sont remplies et vous avertissent quand c’est le cas. Les alertes de métrique sont avec état ; autrement dit, elles envoient uniquement des notifications lorsque l’état change.

## <a name="how-do-metric-alerts-work"></a>Fonctionnement des alertes de métrique

Vous pouvez définir une règle d’alerte de métrique en spécifiant une ressource cible à superviser, un nom de métrique, un type de condition (statique ou dynamique) et la condition (un opérateur et un seuil/une sensibilité), ainsi qu’un groupe d’actions à effectuer lorsque la règle d’alerte se déclenche. Les types de condition affectent la manière de déterminer les seuils. [Découvrez-en plus sur le type de condition des seuils dynamiques et les options de sensibilité](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Règle d’alerte avec le type de condition statique

Supposons que vous avez créé une règle d’alerte de métrique de seuil statique simple comme suit :

- Ressource cible (ressource Azure à surveiller) : myVM
- Mesure : Percentage CPU
- Type de condition : statique
- Agrégation du temps (statistique exécutée sur des valeurs de mesures brutes ; les agrégations de temps prises en charge sont Min, Max, Moy, Total) : Moyenne
- Période (fenêtre de vérification des valeurs de métrique) : Au cours des 5 dernières minutes
- Fréquence (fréquence à laquelle l’alerte de métrique vérifie si les conditions sont remplies) : 1 minute
- Opérateur : Supérieur à
- Seuil : 70

À partir de la création de la règle d’alerte, l’analyse s’exécute chaque minute. Elle examine les valeurs de métrique des 5 dernières minutes et vérifie si la moyenne de ces valeurs est supérieure à 70. Si la condition est remplie (autrement dit, le pourcentage d’UC moyen pour les 5 dernières minutes est supérieur à 70), la règle d’alerte déclenche une notification d’activation. Si vous avez configuré un e-mail ou une action de webhook dans le groupe d’actions associé à la règle d’alerte, vous recevrez une notification sur les deux.

### <a name="alert-rule-with-dynamic-condition-type"></a>Règle d’alerte avec le type de condition dynamique

Supposons que vous avez créé une règle d’alerte de métrique de seuil dynamique simple comme suit :

- Ressource cible (ressource Azure à surveiller) : myVM
- Mesure : Percentage CPU
- Type de condition : Dynamique
- Agrégation du temps (statistique exécutée sur des valeurs de mesures brutes ; les agrégations de temps prises en charge sont Min, Max, Moy, Total) : Moyenne
- Période (fenêtre de vérification des valeurs de métrique) : Au cours des 5 dernières minutes
- Fréquence (fréquence à laquelle l’alerte de métrique vérifie si les conditions sont remplies) : 1 minute
- Opérateur : Supérieur à
- Sensibilité : Moyenne
- Périodes passées à vérifier : 4
- Nombre de violations : 4

Une fois que la règle d’alerte est créée, l’algorithme de machine learning des seuils dynamiques acquiert les données d’historique disponibles, calcule le seuil qui correspond le mieux au modèle de comportement de la série de métriques et continue d’apprendre de nouvelles données pour rendre le seuil plus précis.

À partir du moment où la règle d’alerte est créée, l’analyse s’exécute toutes les minutes et examine les valeurs de métrique des 20 dernières minutes regroupées par période de 5 minutes pour déterminer si la moyenne des valeurs de chacune des 4 périodes dépasse le seuil attendu. Si la condition est remplie, autrement dit, si le pourcentage d’UC moyen des 20 dernières minutes (quatre périodes de 5 minutes) s’est écarté du comportement attendu à quatre reprises, la règle d’alerte déclenche une notification d’activation. Si vous avez configuré un e-mail ou une action de webhook dans le groupe d’actions associé à la règle d’alerte, vous recevrez une notification sur les deux.

### <a name="view-and-resolution-of-fired-alerts"></a>Affichage et résolution des alertes déclenchées

Les exemples ci-dessus de déclenchement des règles d’alerte sont également consultables dans le portail Azure dans le panneau **Toutes les alertes**.

Par exemple, si l’utilisation de « myVM » demeure au-dessus du seuil lors les vérifications suivantes, la règle d’alerte ne sera pas déclenchée à nouveau tant que les conditions ne seront pas résolues.

Au bout d’un moment, l’utilisation de « myVM » revient à la normale (au-dessous du seuil). la règle d’alerte surveille l’état deux fois de plus, puis envoie une notification de résolution. La règle d’alerte envoie un message de résolution/désactivation lorsque la condition d’alerte n’est pas remplie pendant trois périodes consécutives pour réduire le bruit en cas de flottement de conditions.

Comme la notification de résolution est envoyée par le biais de webhooks ou d’un e-mail, l’état de l’instance d’alerte (état de l’analyse) dans le portail Azure est également défini comme résolu.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Surveillance à l’échelle à l’aide d’alertes de métrique dans Azure Monitor

### <a name="using-dimensions"></a>Utilisation de dimensions

Les alertes de métrique dans Azure Monitor prennent également en charge la surveillance de plusieurs combinaisons de valeurs de dimension avec une seule règle. Essayons de comprendre pourquoi vous pouvez utiliser plusieurs combinaisons de dimension à l’aide d’un exemple.

Supposons que vous avez un plan App Service pour votre site web. Vous souhaitez surveiller l’utilisation du processeur sur plusieurs instances de votre site web/application. Vous pouvez le faire à l’aide d’une règle d’alerte de métrique, comme suit :

- Ressource cible : myAppServicePlan
- Mesure : Percentage CPU
- Type de condition : statique
- Dimensions
  - Instance = InstanceName1, InstanceName2
- Agrégation de temps : Moyenne
- Période : Au cours des 5 dernières minutes
- Fréquence : 1 minute
- Opérateur : GreaterThan
- Seuil : 70

Comme précédemment, cette règle contrôle si l’utilisation moyenne du processeur pendant les 5 dernières minutes est supérieure à 70 %. Toutefois, avec la même règle, vous pouvez surveiller deux instances de votre site web. Chaque instance est surveillée individuellement, et vous recevez des notifications séparées.

Admettons que vous avez une application web soumise à une très forte demande et que vous devez ajouter d’autres instances. Cette règle ne surveille toujours que deux instances. Toutefois, vous pouvez créer une règle comme suit :

- Ressource cible : myAppServicePlan
- Mesure : Percentage CPU
- Type de condition : statique
- Dimensions
  - Instance = *
- Agrégation de temps : Moyenne
- Période : Au cours des 5 dernières minutes
- Fréquence : 1 minute
- Opérateur : GreaterThan
- Seuil : 70

Cette règle surveille automatiquement toutes les valeurs pour de l’instance, c’est-à-dire que vous pouvez surveiller les nouvelles instances dès leur ajout sans avoir à modifier votre règle d’alerte de métrique.

Quand vous supervisez plusieurs dimensions, la règle d’alerte de seuil dynamique peut créer des seuils adaptés à des centaines de séries de métriques à la fois. Un seuil dynamique réduit le nombre de règles d’alerte à gérer et le temps passé à gérer et créer ces règles.

Supposons que vous ayez une application web avec de nombreuses instances et que vous ne connaissiez pas le seuil le plus approprié. Les règles ci-dessus utilisent toujours le seuil de 70 %. Toutefois, vous pouvez créer une règle comme suit :

- Ressource cible : myAppServicePlan
- Mesure : Percentage CPU
- Type de condition : Dynamique
- Dimensions
  - Instance = *
- Agrégation de temps : Moyenne
- Période : Au cours des 5 dernières minutes
- Fréquence : 1 minute
- Opérateur : GreaterThan
- Sensibilité : Moyenne
- Périodes passées à vérifier : 1
- Nombre de violations : 1

Cette règle surveille si l’utilisation moyenne du processeur pendant les 5 dernières minutes dépasse le comportement attendu pour chaque instance. Vous pouvez superviser les instances dès leur ajout sans avoir à remodifier votre règle d’alerte de métrique. Chaque instance obtient un seuil qui s’adapte au modèle de comportement de la série de métriques et qui change en permanence selon les nouvelles données pour rendre le seuil plus précis. Comme avant, chaque instance est supervisée individuellement et vous recevez des notifications séparées.

L’allongement des périodes passées à vérifier et le nombre de violations peuvent aussi vous permettre de filtrer les alertes de sorte à seulement recevoir une conformément à votre définition d’un écart significatif. [Découvrez-en plus sur les options avancées des seuils dynamiques](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Supervision de plusieurs ressources à l’aide d’alertes de métrique

Comme vous l’avez vu dans la section précédente, il est possible d’avoir une seule règle d’alerte de métrique unique qui surveille chaque combinaison de dimensions (par exemple, une série chronologique de métriques). Toutefois, vous étiez auparavant toujours contraint de n’effectuer l’opération que sur une seule ressource à la fois. Azure Monitor prend également en charge la supervision de plusieurs ressources avec une seule règle d’alerte de métrique. Cette fonctionnalité est actuellement disponible en préversion et n’est prise en charge que sur les machines virtuelles. En outre, une alerte de métrique unique ne peut superviser les ressources que dans une seule région Azure.

Vous disposez de trois méthodes pour spécifier l’étendue de la supervision par une alerte de métrique unique :

- sous la forme d’une liste de machines virtuelles dans une seule région Azure d’un abonnement ;
- pour toutes les machines virtuelles (dans une seule région Azure) dans un ou plusieurs groupes de ressources d’un abonnement ;
- pour toutes les machines virtuelles (dans une seule région Azure) d’un abonnement.

La création de règles d’alerte de métrique surveillant plusieurs ressources n’est pas actuellement prise en charge via le portail Azure. Vous pouvez créer ces règles par le biais des [modèles Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Vous recevrez des notifications individuelles pour chaque machine virtuelle.

## <a name="typical-latency"></a>Latence classique

Pour les alertes de métrique, vous êtes généralement informé en moins de 5 minutes si vous définissez la fréquence de règle d’alerte sur 1 minute. En cas de forte charge sur les systèmes de notification, vous pouvez observer une latence plus longue.

## <a name="supported-resource-types-for-metric-alerts"></a>Types de ressources pris en charge pour les alertes de métrique

Vous trouverez la liste complète des types de ressources pris en charge dans cet [article](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Si vous utilisez aujourd'hui des alertes de métrique classiques et cherchez à savoir si les alertes de métrique prennent en charge tous les types de ressources que vous utilisez, le tableau suivant répertorie les types de ressources pris en charge par les alertes de métrique classiques et indique s'ils sont actuellement pris en charge ou non.

|Type de ressource pris en charge par les alertes de métrique classiques | Pris en charge par les alertes de métrique |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Oui |
| Microsoft.Batch/batchAccounts| Oui|
|Microsoft.Cache/redis| Oui
|Microsoft.ClassicCompute/virtualMachines | Non  |
|Microsoft.ClassicCompute/domainNames/slots/roles | Non |
|Microsoft.CognitiveServices/accounts | Non  |
|Microsoft.Compute/virtualMachines | Oui|
|Microsoft.Compute/virtualMachineScaleSets| Oui|
|Microsoft.ClassicStorage/storageAccounts| Non  |
|Microsoft.DataFactory/datafactories | Oui|
|Microsoft.DBforMySQL/servers| Oui|
|Microsoft.DBforPostgreSQL/servers| Oui|
|Microsoft.Devices/IotHubs | Non |
|Microsoft.DocumentDB/databaseAccounts| Non |
|Microsoft.EventHub/namespaces | Oui|
|Microsoft.Logic/workflows | Oui|
|Microsoft.Network/loadBalancers |Oui|
|Microsoft.Network/publicIPAddresses| Oui|
|Microsoft.Network/applicationGateways| Oui|
|Microsoft.Network/expressRouteCircuits| Oui|
|Microsoft.Network/trafficManagerProfiles | Oui|
|Microsoft.Search/searchServices | Non |
|Microsoft.ServiceBus/namespaces| Non |
|Microsoft.Storage/storageAccounts | Oui|
|Microsoft.StreamAnalytics/streamingjobs| Oui|
|Microsoft.TimeSeriesInsights/environments | Oui|
|Microsoft. Web/serverfarms | Oui |
|Microsoft. Web/sites (à l’exclusion de Functions) | Oui|
|Microsoft. Web/hostingEnvironments/multiRolePools | Non |
|Microsoft. Web/hostingEnvironments/workerPools| Non 
|Microsoft.SQL/Servers | Non |

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir comment créer, afficher et gérer des alertes de métrique dans Azure](alerts-metric.md)
- [Découvrir comment déployer des alertes de métrique à l’aide de modèles Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [En savoir plus sur les groupes d’actions](action-groups.md)
- [En savoir plus sur le type de condition des seuils dynamiques](alerts-dynamic-thresholds.md)
