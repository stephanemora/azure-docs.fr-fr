---
title: Préparer la migration des alertes classiques Azure Monitor en mettant à jour vos applications logiques et les procédures opérationnelles
description: Découvrez comment modifier votre webhook, application logique et les procédures opérationnelles pour préparer la migration volontaire.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346873"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Préparer vos applications logiques et d’exécuter la documentation pour la migration de règles d’alerte classique

En tant que [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront retirés dans juillet 2019. L’outil de migration pour déclencher volontairement de migration est disponible dans le portail Azure et est en cours pour les clients qui utilisent des règles d’alerte classiques.

Si vous choisissez de migrer volontairement vos règles d’alerte classiques pour les nouvelles règles d’alerte, il existe certaines différences entre les deux systèmes dont vous devez être conscient de. Cet article vous guidera à travers les différences entre les deux systèmes et comment vous pouvez préparer pour la modification.

## <a name="api-changes"></a>Modifications d'API

Les API utilisées pour créer et gérer des règles d’alerte classiques (`microsoft.insights/alertrules`) diffère de l’API utilisées pour créer et gérer de nouvelles alertes de métrique (`microsoft.insights/metricalerts`). Si vous par programmation créez et gérez des règles d’alerte classiques dès aujourd'hui, mettez à jour vos scripts de déploiement pour travailler avec les nouvelles API.

Le tableau suivant fournit une référence à des interfaces de programmation pour les alertes classiques et nouveaux.

|         |Alertes classiques  |Nouvelles alertes de métrique |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [alerte de métriques de moniteur AZ](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Référence](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Modèle Azure Resource Manager | [Pour les alertes classiques](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Nouvelles alertes de métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Modifications de charge utile de notification

Le format de charge utile de notification est légèrement différent entre [des règles d’alerte classiques](alerts-webhooks.md) et [nouvelles alertes de métrique](alerts-metric-near-real-time.md#payload-schema). Si vous avez un webhook, l’application logique ou les actions de runbook qui est déclenchées par des règles d’alerte classiques, vous devez mettre à jour ces points de terminaison de notification pour accepter le format de charge utile de nouvelles alertes de métrique.

Vous pouvez utiliser le tableau suivant pour mapper les champs entre la charge utile du webhook règle d’alerte classique et la nouvelle charge utile de webhook d’alerte métrique.

|  |Alertes classiques  |Nouvelles alertes de métrique |
|---------|---------|---------|
|L’alerte a été activée ou résolue     | status       | data.status |
|Informations contextuelles sur l’alerte     | context        | data.context        |
|Horodatage à laquelle l’alerte a été activée ou résolue      | context.timestamp       | data.context.timestamp        |
| ID de règle d’alerte | context.id | data.context.id |
| Nom de la règle d’alerte | context.name | data.context.name |
| Description de la règle d’alerte | context.description | data.context.description |
| Condition de règle d’alerte | context.condition | data.context.condition|
| Nom de métrique | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Agrégation de temps (comment la métrique est agrégée sur la fenêtre d’évaluation)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Période d’évaluation | context.condition.windowSize | data.context.condition.windowSize|
| Opérateur (comment la valeur de métrique agrégée est comparée au seuil) | context.condition.operator | data.context.condition.operator|
| Seuil | context.condition.threshold| data.context.condition.allOf[0].threshold|
| Valeur de métrique | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| Identifiant d’abonnement | context.subscriptionId | data.context.subscriptionId|
| Groupe de ressources de la ressource affectée | context.resourceGroup | data.context.resourceGroup|
| Nom de la ressource affectée | context.resourceName | data.context.resourceName |
| Type de la ressource affectée | context.resourceType | data.context.resourceType |
|  ID de ressource de la ressource affectée | context.resourceId | data.context.resourceId |
| Un lien direct vers la page de résumé des ressources du portail | context.portalLink | data.context.portalLink|
| Champs de charge utile personnalisée à passer au webhook ou une application logique | properties |Data.Properties |

Comme vous pouvez le voir, les deux les charges utiles sont similaires. Section suivante a plus d’informations sur les exemples d’applications logique et un exemple de runbook pour analyser la charge utile de notification pour les nouvelles alertes.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>À l’aide d’une application logique qui reçoit une notification d’alerte métrique

Si vous utilisez des applications logiques avec des alertes classiques, vous devrez modifier votre application logique pour analyser la charge utile de nouvelles alertes de métrique.

1. Créer une application logique.

2. Utilisez le modèle « Azure Monitor – métriques alerte gestionnaire ». Ce modèle a un **demande HTTP** déclencheur avec le schéma approprié défini

    ![modèle d’application logique](media/alerts-migration/logic-app-template.png "modèle d’alerte métrique")

3. Ajouter une action pour héberger votre logique de traitement.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>À l’aide d’un runbook automation qui reçoit une notification d’alerte métrique

L’exemple suivant présente le code PowerShell qui peut être utilisée dans votre runbook qui peut analyser les charges utiles pour les règles d’alerte de métrique classiques et de nouvelles règles d’alerte métrique.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Consultez un exemple complet d’un runbook qui arrête une machine virtuelle lorsqu’une alerte est déclenchée dans [documentation d’Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Intégration des partenaires par le biais de webhooks

La plupart des [nos partenaires qui s’intègrent avec les alertes classiques](https://docs.microsoft.com/azure/azure-monitor/platform/partners) prennent déjà en charge les alertes métriques plus récentes via leurs intégrations. Intégrations connues qui fonctionnent déjà avec les nouvelles alertes de métrique sont répertoriées ci-dessous.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Si vous utilisez une intégration des partenaires qui n’est pas répertoriée ici, vérifiez auprès du fournisseur d’intégration que l’intégration fonctionne avec les nouvelles alertes de métrique.

## <a name="next-steps"></a>Étapes suivantes

- [Comment utiliser l’outil de migration](alerts-using-migration-tool.md)
- [Comprendre le fonctionne de l’outil de migration](alerts-understand-migration.md)
