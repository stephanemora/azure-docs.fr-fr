---
title: Surveiller les métriques de Media Services et des journaux de diagnostic via Azure Monitor | Microsoft Docs
description: Cet article donne une vue d’ensemble de la surveillance des métriques de Media Services et des journaux de diagnostic via Azure Monitor.
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
ms.openlocfilehash: 7ce57e1f8b2732ea909625c89f3e8148cb70635c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728839"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Surveiller les métriques de Media Services et des journaux de diagnostic

[Azure Monitor](../../azure-monitor/overview.md) permet aux mesures de surveillance et de journaux de diagnostic qui vous aident à comprendre le fonctionnement de vos applications. Toutes les données collectées par Azure Monitor s’adapte à un des deux types fondamentaux, journaux et métriques. Vous pouvez surveiller les journaux de diagnostic de Media Services et créer des alertes et notifications pour les métriques collectées et les journaux. Vous pouvez visualiser et analyser les données de mesures à l’aide [Metrics explorer](../../azure-monitor/platform/metrics-getting-started.md). Vous pouvez envoyer des journaux au [stockage Azure](https://azure.microsoft.com/services/storage/), les transmettre à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)et les exporter vers [Analytique de journal](https://azure.microsoft.com/services/log-analytics/), ou utiliser les services tiers 3e.

Pour une présentation détaillée, consultez [mesures Azure Monitor](../../azure-monitor/platform/data-platform.md) et [journaux de Diagnostic du moniteur Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Cette rubrique traite actuellement disponibles [Media Services métriques](#media-services-metrics) et [journaux de Diagnostic de Media Services](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Métriques de Media Services

Les métriques sont collectées à intervalles réguliers, que la valeur change ou non. Elles sont utiles pour créer des alertes, dans la mesure où elles peuvent être échantillonnées fréquemment, et où une alerte peut être déclenchée rapidement avec un circuit logique assez simple.

Actuellement, les Services de média suivants [les points de terminaison de diffusion en continu](https://docs.microsoft.com/rest/api/media/streamingendpoints) métriques sont émis par Azure :

|Nom|Description|
|---|---|
|Demandes|Donne des détails concernant le nombre total de demandes traitées par le point de terminaison de diffusion en continu.|
|Sortie|Nombre total d’octets de sortie. Par exemple, les octets envoyés par le point de terminaison de diffusion en continu.|
|Latence de bout en bout de réussite| Fournit des informations sur la latence de bout en bout de demandes réussies.|

Par exemple, pour obtenir des mesures de « Sortie » avec l’interface CLI, exécutez la commande suivante `az monitor metrics` commande CLI :

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Pour plus d’informations sur la création d’alertes de métriques, consultez [créer, afficher et gérer les alertes de métriques avec Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Journaux de diagnostic de Media Services

Actuellement, vous pouvez obtenir les journaux de diagnostics suivants :

|Nom|Description|
|---|---|
|Demande de service de remise de clé|Journaux qui indiquent les informations de demande de service de remise de clé. Pour plus d’informations, consultez [schémas](media-services-diagnostic-logs-schema.md).|

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, vous exécuteriez suit `az monitor diagnostic-settings` commande CLI : 

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

[Comment collecter et consommer des données de journal à partir de vos ressources Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
