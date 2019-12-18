---
title: Schémas des journaux de diagnostic Azure Media Services - Azure
description: Cet article présente les schémas des journaux de diagnostic d’Azure Media Services.
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
ms.openlocfilehash: 5e416087df6c27eff42db487e90ff908d7d49df8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977690"
---
# <a name="diagnostic-logs-schemas"></a>Schémas des journaux de diagnostic

[Azure Monitor](../../azure-monitor/overview.md) vous permet de surveiller les métriques et les journaux de diagnostic pour vous aider à comprendre le fonctionnement de vos applications. Vous pouvez surveiller les journaux de diagnostic de Media Services et créer des alertes et des notifications pour les métriques et les journaux collectés. Vous pouvez envoyer les journaux d’activité au [Stockage Azure](https://azure.microsoft.com/services/storage/), les transmettre à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et les exporter vers [Log Analytics](https://azure.microsoft.com/services/log-analytics/) ou utiliser un service externe.

Pour plus d’informations, consultez [Métriques Azure Monitor](../../azure-monitor/platform/data-platform.md) et [Journaux de diagnostic Azure Monitor](../../azure-monitor/platform/resource-logs-overview.md).

Cet article décrit les schémas des journaux de diagnostic de Media Services.

## <a name="top-level-diagnostic-logs-schema"></a>Schéma des journaux de diagnostic de niveau supérieur

Pour une description détaillée du schéma général des journaux de diagnostic, consultez [Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Schéma de journal de remise de clé

### <a name="properties"></a>properties

Ces propriétés sont spécifiques au schéma du journal de remise de clé.

|Nom|Description|
|---|---|
|keyId|ID de la clé demandée.|
|keyType|Peut être l’une des valeurs suivantes : « Clear » (aucun chiffrement), « FairPlay », « PlayReady » ou « Widevine ».|
|policyName|Nom de la stratégie Azure Resource Manager.|
|tokenType|Le type de jeton.|
|statusMessage|L’état du message.|

### <a name="examples"></a>Exemples

Propriétés du schéma de demande de remise de clé.

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

## <a name="additional-notes"></a>Remarques supplémentaires

* Widevine est un service fourni par Google Inc. soumis aux conditions de service et à la politique de confidentialité de Google, Inc.

## <a name="next-steps"></a>Étapes suivantes

[Superviser les métriques et les journaux de diagnostic Media Services](media-services-metrics-diagnostic-logs.md)
