---
title: Référence sur la surveillance des données de Media Services
description: Matériel de référence important nécessaire pour analyser Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.date: 03/11/2021
ms.openlocfilehash: 461c998aa85d70d69cb267fdbeabd7eabcfb5854
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471550"
---
# <a name="monitoring-media-services-data-reference"></a>Référence sur la surveillance des données de Media Services

Cet article décrit les données qui sont utiles pour la surveillance des Media Services. Pour plus d’informations sur les métriques de plateforme prises en charge dans Azure Monitor, consultez [Métriques prises en charge avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

## <a name="media-services-metrics"></a>Métriques Media Services

Les métriques sont collectées à intervalles réguliers, que la valeur change ou non. Elles sont utiles pour créer des alertes, dans la mesure où elles peuvent être échantillonnées fréquemment, et où une alerte peut être déclenchée rapidement avec un circuit logique assez simple.

Media Services prend en charge les métriques de surveillance pour les ressources suivantes :

* Compte
* Point de terminaison de streaming

### <a name="account"></a>Compte

Vous pouvez surveiller les métriques de compte suivantes.

|Nom de métrique|Nom complet|Description|
|---|---|---|
|AssetCount|Nombre de ressources|Ressources dans votre compte.|
|AssetQuota|Quota de ressources|Quota de ressources dans votre compte.|
|AssetQuotaUsedPercentage|Pourcentage du quota de ressources utilisé|Pourcentage du quota de ressources déjà utilisé.|
|ContentKeyPolicyCount|Nombre de stratégies de clé de contenu|Stratégies de clé de contenu dans votre compte.|
|ContentKeyPolicyQuota|Quota de stratégies de clé de contenu|Quota de stratégies de clé de contenu dans votre compte.|
|ContentKeyPolicyQuotaUsedPercentage|Pourcentage du quota de stratégies de clé de contenu utilisé|Pourcentage du quota de stratégies de clé de contenu déjà utilisé.|
|StreamingPolicyCount|Nombre de stratégies de diffusion en continu|Stratégies de diffusion en continu dans votre compte.|
|StreamingPolicyQuota|Quota de stratégies de diffusion en continu|Quota de stratégies de diffusion en continu dans votre compte.|
|StreamingPolicyQuotaUsedPercentage|Pourcentage du quota de stratégies de diffusion en continu utilisé|Pourcentage du quota de stratégies de diffusion en continu déjà utilisé.|

Vous devez également examiner les [quotas et limites de compte](../limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Point de terminaison de streaming

Les métriques de [points de terminaison de streaming](/rest/api/media/streamingendpoints) Media Services suivantes sont pris en charge :

|Nom de métrique|Nom complet|Description|
|---|---|---|
|Demandes|Demandes|Fournit le nombre total de requêtes HTTP traitées par le point de terminaison de streaming.|
|Sortie|Sortie|Nombre total d’octets de sortie par minute par point de terminaison de streaming.|
|SuccessE2ELatency|Latence de réussite de bout en bout|Durée à partir de laquelle le point de terminaison de streaming a reçu la requête lorsque le dernier octet de la réponse a été envoyé.|
|Utilisation de l’UC| | Utilisation de l’UC pour les points de terminaison de streaming Premium. Ces données ne sont pas disponibles pour les points de terminaison de streaming Standard. |
|Bande passante en sortie | | Sortie de la bande passante en bits par seconde.|

## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Journaux d’activité de ressources

## <a name="media-services-diagnostic-logs"></a>Journaux de diagnostic Media Services

Les journaux de diagnostic fournissent des informations complètes et fréquentes sur le fonctionnement d’une ressource Azure. Pour plus d’informations, consultez [Comment collecter et utiliser des données de journal à partir de vos ressources Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md).

Media Services prend en charge les journaux de diagnostic suivants :

* Remise de clés

### <a name="key-delivery"></a>Remise de clés

|Nom|Description|
|---|---|
|Demande de service de remise de clé|Journaux contenant les informations de demande de service de remise de clé. Pour plus d’informations, consultez la section relative aux [schémas](monitor-media-services-data-reference.md).|

## <a name="schemas"></a>Schémas

Pour une description détaillée du schéma général des journaux de diagnostic, consultez [Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Propriétés du schéma de journal de remise de clés

Ces propriétés sont spécifiques au schéma du journal de remise de clé.

|Name|Description|
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
