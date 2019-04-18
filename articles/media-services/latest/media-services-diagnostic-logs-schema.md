---
title: Schémas - Azure des journaux de diagnostic Azure Media Services
description: Cet article explique les schémas de journaux de diagnostic Azure Media Services.
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
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850632"
---
# <a name="diagnostic-logs-schemas"></a>Schémas des journaux de diagnostic

[Azure Monitor](../../azure-monitor/overview.md) permet aux mesures de surveillance et de journaux de diagnostic qui vous aident à comprendre le fonctionnement de vos applications. Vous pouvez surveiller les journaux de diagnostic de Media Services et créer des alertes et notifications pour les métriques collectées et les journaux. Vous pouvez envoyer des journaux au [stockage Azure](https://azure.microsoft.com/services/storage/), les transmettre à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)et les exporter vers [Analytique de journal](https://azure.microsoft.com/services/log-analytics/), ou utiliser les services tiers 3e.

Pour plus d’informations, consultez [mesures Azure Monitor](../../azure-monitor/platform/data-platform.md) et [journaux de Diagnostic du moniteur Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Cet article décrit les schémas des journaux de diagnostic de Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Schéma des journaux de diagnostic de niveau supérieur

Pour obtenir une description détaillée du schéma de niveau supérieur de journaux de diagnostic, consultez [prise en charge des services, des schémas et des catégories pour les journaux de Diagnostic Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schéma de journal de remise de clé

### <a name="properties"></a>properties

Ces propriétés sont spécifiques au schéma de journal de remise de clé.

|Nom|Description|
|---|---|
|keyId|ID de la clé demandée.|
|keyType|Peut être l’une des valeurs suivantes : « Clear » (aucun chiffrement), « FairPlay », « PlayReady » ou « Widevine ».|
|policyName|Nom de la stratégie Azure Resource Manager.|
|tokenType|Le type de jeton.|
|statusMessage|Le message d’état.|

### <a name="examples"></a>Exemples

Propriétés du schéma de demandes de remise de clé.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>Étapes suivantes

[Surveiller les métriques de Media Services et des journaux de diagnostic](media-services-metrics-diagnostic-logs.md)
