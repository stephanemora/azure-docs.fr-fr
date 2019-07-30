---
title: Surveiller les métriques et journaux de diagnostic Media Services via Azure Monitor | Microsoft Docs
description: Cet article donne un aperçu de la façon de surveiller les métriques et journaux de diagnostic Media Services via Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: bbf43ecb07947fad8cc1ee064d2038e4a21d4444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964772"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Surveiller les métriques et journaux de diagnostic Media Services

[Azure Monitor](../../azure-monitor/overview.md) vous permet de surveiller les métriques et journaux de diagnostic pour vous aider à comprendre le fonctionnement de vos applications. Toutes les données collectées par Azure Monitor sont de l’un des deux types fondamentaux, les métriques et les journaux d’activité. Vous pouvez surveiller les journaux de diagnostic Media Services et créer des alertes et notifications pour les métriques et les journaux collectés. Vous pouvez visualiser et analyser les métriques à l’aide [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). Vous pouvez envoyer les journaux d’activité au [Stockage Azure](https://azure.microsoft.com/services/storage/), les transmettre à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et les exporter vers [Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou utiliser un service externe.

Pour plus un aperçu détaillé, voir [Métriques Azure Monitor](../../azure-monitor/platform/data-platform.md) et [Journaux de diagnostic Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

Cette rubrique traite des [métriques Media Services](#media-services-metrics) et des [Journaux de diagnostic Media Services](#media-services-diagnostic-logs) actuellement disponibles.

## <a name="media-services-metrics"></a>Métriques Media Services

Les métriques sont collectées à intervalles réguliers, que la valeur change ou non. Elles sont utiles pour créer des alertes, dans la mesure où elles peuvent être échantillonnées fréquemment, et où une alerte peut être déclenchée rapidement avec un circuit logique assez simple.

Actuellement, les métriques de [points de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) Media Services suivantes sont émises par Azure :

|Métrique|Nom complet|Description|
|---|---|---|
|Demandes|Demandes|Fournit des détails sur le nombre total de demandes traitées par le point de terminaison de streaming.|
|Sortie|Sortie|Nombre total d’octets de sortie. Par exemple, les octets diffusés en continu par le point de terminaison de streaming.|
|SuccessE2ELatency|Latence de réussite de bout en bout| Fournit des informations sur la latence de bout en bout des demandes réussies.|

Par exemple, pour obtenir des métriques de « Sortie » avec Azure CLI, vous devez exécuter la commande `az monitor metrics` suivante :

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Pour plus d’informations sur la création d’alertes de métrique, voir [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Journaux de diagnostic Media Services

Actuellement, vous pouvez obtenir les journaux de diagnostic suivants :

|Nom|Description|
|---|---|
|Demande de service de remise de clé|Journaux contenant les informations de demande de service de remise de clé. Pour plus d’informations, voir [Schémas](media-services-diagnostic-logs-schema.md).|

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, exécutez la commande `az monitor diagnostic-settings` suivante : 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Par exemple :

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Étapes suivantes 

[Comment collecter et utiliser les données de journal de vos ressources Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
