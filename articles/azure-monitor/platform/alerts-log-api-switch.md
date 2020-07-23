---
title: Basculer vers la nouvelle API Alertes Azure
description: Vue d'ensemble de l'API Alerte héritée et basée sur savedSearch de Log Analytics, et processus permettant de basculer des règles d'alerte vers la nouvelle API ScheduledQueryRules, avec des informations répondant aux préoccupations des clients.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: a5613198b404f629727f3040b308c030763a10b2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515799"
---
# <a name="switch-api-preference-for-log-alerts"></a>Changer de préférence d'API pour les alertes de journal

> [!NOTE]
> Contenu stipulé applicable aux utilisateurs du cloud public Azure uniquement et **pas** pour le cloud Azure Government ni Azure Chine.  

> [!NOTE]
> Une fois qu’un utilisateur choisit de changer la préférence pour la nouvelle [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules), il est impossible de revenir à l’utilisation de l’ancienne [API Alerte héritée de Log Analytics](api-alerts.md).

Récemment encore, vous gériez les règles d'alerte à partir du portail Microsoft Operations Management Suite. La nouvelle expérience d'alerte a été intégrée à différents services de Microsoft Azure, dont Log Analytics, et nous avons demandé une [extension de vos règles d'alerte du portail OMS vers Azure](./alerts-unified-log.md). Cela dit, afin que les clients subissent le moins de perturbations possible, le processus n'a pas modifié l'interface de programmation - [API Alerte de Log Analytics](api-alerts.md) basée sur SavedSearch.

Mais aujourd'hui, vous proposez aux utilisateurs d'alertes de Log Analytics une véritable alternative de programmation Azure, l'[API ScheduledQueryRules - Azure Monitor](/rest/api/monitor/scheduledqueryrules), également reflétée sur votre [facturation Azure - pour les alertes de journal](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Pour en savoir plus sur la gestion des alertes de journal à l'aide de l'API, consultez [Gestion des alertes de journal à l’aide du modèle de ressource Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) et [Gestion des alertes de journal à l’aide de PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Avantages du passage à la nouvelle API Azure

L'utilisation de l'[API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) au détriment de l'[API Alerte héritée de Log Analytics](api-alerts.md) pour créer et gérer des alertes présente différents avantages. Ces principaux avantages sont les suivants :

- Possibilité de mener dans les règles d'alerte des [recherches croisées dans les journaux de l'espace de travail](../log-query/cross-workspace-query.md) et d'utiliser des ressources externes telles que des espaces de travail Log Analytics ou même des applications Application Insights.
- Lorsque plusieurs champs sont utilisés pour effectuer un regroupement dans une requête, l'[API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) peut permettre à l'utilisateur de spécifier les champs à agréger sur le portail Azure.
- Les alertes de journal créées à l’aide de l’API [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) peuvent couvrir une période de 48 heures maximum et récupérer des données antérieures.
- Possibilité de créer simultanément plusieurs règles d'alerte en tant que ressource unique sans qu'il soit nécessaire de créer trois niveaux de ressources comme avec l'[API Alerte hébergée de Log Analytics](api-alerts.md).
- Interface de programmation unique pour toutes les variantes des alertes de journal basées sur des requêtes d'Azure - la nouvelle [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) peut être utilisée pour gérer les règles de Log Analytics et d'Application Insights.
- Gestion de vos alertes de journal à l’aide des [applets de commande Powershell](alerts-log.md#managing-log-alerts-using-powershell)
- Les nouvelles fonctionnalités d'alerte de journal et les futurs développements seront uniquement disponibles via la nouvelle [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules).

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processus de basculement à partir de l'API Alertes de journal héritée

Les utilisateurs sont libres d'utiliser l'[API Alerte héritée de Log Analytics](api-alerts.md) ou la nouvelle [API scheduleQueryRules](/rest/api/monitor/scheduledqueryrules). Les règles d'alerte créées par l'une ou l'autre de ces API devront être *gérées par la même API*, ainsi que sur le portail Azure. Par défaut, Azure Monitor continuera à utiliser l'[API Alerte héritée de Log Analytics](api-alerts.md) pour créer de nouvelles règles d'alerte à partir du portail Azure pour les espaces de travail existants de Log Analytics. En tant que [nouvel espace de travail de journal annoncé, créé le ou après le 1er juin 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) -la nouvelle [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) sera automatiquement utilisée par défaut, y compris dans le portail Azure.

Les conséquences du passage à l'API scheduleQueryRules sont compilées ci-dessous :

- Toutes les interactions liées à la gestion des alertes de journal via des interfaces de programmation doivent désormais s'effectuer à l'aide de l'API [scheduleQueryRules](/rest/api/monitor/scheduledqueryrules). Pour plus d'informations, consultez [Exemple d'utilisation via Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) et [Exemple d'utilisation via PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Toute nouvelle règle d'alerte de journal créée sur le portail Azure devra utiliser l'API [scheduleQueryRules](/rest/api/monitor/scheduledqueryrules). Les utilisateurs auront également la possibilité d'utiliser la [fonctionnalité supplémentaire de la nouvelle API](#benefits-of-switching-to-new-azure-api) via le portail Azure.
- La gravité pour les règles d’alerte de journal augmentera de : *critique, avertissement ou information* à *Valeurs de gravité de 0, 1 et 2*. Avec la possibilité de créer/mettre à jour les règles d’alerte également avec la gravité 3 et 4.

Le processus de déplacement des règles d'alerte depuis l'[API Alerte héritée de Log Analytics](api-alerts.md) n'implique aucune modification, de quelque nature que ce soit, de la définition, de la requête ou de la configuration de votre alerte. Vos règles d’alerte et votre surveillance ne sont pas affectées et les alertes ne s’arrêteront pas et ne seront pas bloquées, pendant ou après le basculement. Les seules modifications sont les suivantes :

- Une modification de la préférence d’API et de l’accès à vos règles via une nouvelle API.
- Un URI de ressource de règle d’alerte modifié contenant les ID utilisés dans l’[API Alerte héritée de Log Analytics](api-alerts.md) au lieu du nom de la règle d’alerte dans cette structure `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. Le nom d’affichage de la règle d’alerte reste inchangé.

Tout client souhaitant passer volontairement à la nouvelle API [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) et bloquer l’utilisation de l’[API Alerte héritée de Log Analytics](api-alerts.md) peut le faire via un appel PUT sur l’API ci-dessous afin de modifier toutes les règles d’alerte associées à l’espace de travail Log Analytics spécifique.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Avec corps de la demande contenant le code JSON ci-dessous.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

L’API est également accessible à partir d’une ligne de commande PowerShell en utilisant [ARMClient](https://github.com/projectkudu/ARMClient), outil en ligne de commande open source qui simplifie l’appel de l’API Azure Resource Manager. Comme illustré ci-dessous, dans l’exemple d’appel PUT utilisant l’outil ARMclient pour basculer toutes les règles d’alerte associées à l’espace de travail Log Analytics spécifique.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Si le passage de toutes les règles d’alerte de l’espace de travail Log Analytics à la nouvelle API [scheduleQueryRules](/rest/api/monitor/scheduledqueryrules) aboutit, la réponse suivante est fournie.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Les utilisateurs peuvent également vérifier l’état actuel de votre espace de travail Log Analytics et voir s’il a été basculé ou non vers [scheduleQueryRules](/rest/api/monitor/scheduledqueryrules). Cette vérification peut être effectuée via un appel GET sur l'API ci-dessous.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pour exécuter ce qui précède en utilisant la ligne de commande PowerShell avec l’outil [ARMClient](https://github.com/projectkudu/ARMClient), consultez l’exemple ci-après.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Si l’espace de travail Log Analytics spécifié a été basculé vers [scheduleQueryRules](/rest/api/monitor/scheduledqueryrules), le code JSON de la réponse se présentera comme suit.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Dans le cas contraire, si l'espace de travail Log Analytic spécifié n'a pas encore été basculé vers [scheduleQueryRules](/rest/api/monitor/scheduledqueryrules), le code JSON de la réponse se présentera comme suit.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Monitor - Alertes de journal](alerts-unified-log.md).
- En savoir plus sur la création d’[alertes de journal dans Alertes Azure](alerts-log.md).
- En savoir plus sur l’[expérience Alertes Azure](../../azure-monitor/platform/alerts-overview.md).
