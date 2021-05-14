---
title: Référence sur la surveillance des données de Media Services
description: Matériel de référence important nécessaire pour analyser Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: a9bc17528e1314a033cf2e45fee4b112eb088cc3
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715571"
---
# <a name="monitoring-media-services-data-reference"></a>Référence sur la surveillance des données de Media Services

Cet article décrit les données qui sont utiles pour la surveillance des Media Services. Pour plus d’informations sur les métriques de plateforme prises en charge dans Azure Monitor, consultez [Métriques prises en charge avec Azure Monitor](../../../azure-monitor/essentials/metrics-supported.md).

## <a name="metrics"></a>Métriques

Les métriques sont collectées à intervalles réguliers, que la valeur change ou non. Elles sont utiles pour créer des alertes, dans la mesure où elles peuvent être échantillonnées fréquemment, et où une alerte peut être déclenchée rapidement avec un circuit logique assez simple.


Media Services prend en charge les métriques de surveillance pour les ressources suivantes :

|Type de métrique | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Généralités concernant Media Services | [Général](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservices) |
| Événements en direct | [Microsoft.Media/mediaservices/liveEvents](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesliveevents) 
| Points de terminaison de diffusion en continu | [Microsoft.Media/mediaservices/streamingEndpoints](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints), qui ont trait à l’[API REST Points de terminaison de streaming](/rest/api/media/streamingendpoints). 


Vous devez également examiner les [quotas et limites de compte](../limits-quotas-constraints-reference.md).


## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Media Services présente les dimensions de métriques suivantes.  Elles sont explicites selon les métriques prises en charge.  Pour plus d’informations, consultez les [Liens de métriques](#metrics) ci-dessus.   
- OutputFormat
- HttpStatusCode 
- ErrorCode 
- TrackName 

## <a name="resource-logs"></a>Journaux d’activité de ressources

Les journaux de ressources fournissent des informations complètes et fréquentes sur le fonctionnement d’une ressource Azure. Pour plus d’informations, consultez [Comment collecter et utiliser des données de journal à partir de vos ressources Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

Media Services prend en charge les journaux de ressources suivants : [Microsoft.Media/mediaservices](../../../azure-monitor/essentials/resource-logs-categories.md#microsoftmediamediaservices)

## <a name="schemas"></a>Schémas

Pour une description détaillée du schéma général des journaux de diagnostic, consultez [Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure](../../../azure-monitor/essentials/resource-logs-schema.md).

### <a name="key-delivery"></a>Remise de clés 

Ces propriétés sont spécifiques au schéma du journal de remise de clé.

|Nom|Description|
|---|---|
|keyId|ID de la clé demandée.|
|keyType|Peut prendre l’une des valeurs suivantes : « Clear » (aucun chiffrement), « FairPlay », « PlayReady » ou « Widevine ».|
|policyName|Nom de la stratégie Azure Resource Manager.|
|tokenType|Type de jeton.|
|statusMessage|L’état du message.|

### <a name="example"></a>Exemple

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

>[!NOTE]
> Widevine est un service fourni par Google Inc. soumis aux conditions de service et à la politique de confidentialité de Google, Inc.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
