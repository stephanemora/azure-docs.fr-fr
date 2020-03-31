---
title: Obtenir des événements d’intégrité des ressources Azure à l’aide de l’API REST | Microsoft Docs
description: Utilisez l’API REST Azure pour obtenir les événements d’intégrité de vos ressources Azure.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653999"
---
# <a name="get-resource-health-using-the-rest-api"></a>Obtenir des événements d’intégrité des ressources Azure à l’aide de l’API REST 

Cet article montre comment récupérer une liste des événements d’intégrité des ressources Azure dans votre abonnement à l’aide de [l’API REST Azure](/rest/api/azure/).

Pour consulter la documentation de référence complète et d’autres exemples avec l’API REST, reportez-vous à [Informations de référence sur l’API REST Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Générer la demande

Utilisez la requête HTTP `GET` suivante pour lister les événements d’intégrité des ressources de votre abonnement pour la période comprise entre `2018-05-16` et `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>En-têtes de requête

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Obligatoire. Défini sur `application/json`.|  
|*Authorization :*|Obligatoire. Défini sur un [jeton d’accès](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valide. |  

### <a name="uri-parameters"></a>Paramètres URI

| Nom | Description |
| :--- | :---------- |
| subscriptionId | L’ID d’abonnement d’un abonnement Azure. Si vous avez plusieurs abonnements, consultez [Utilisation de plusieurs abonnements](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| api-version | Version de l’API à utiliser pour la requête.<br /><br /> Ce document aborde la version d’API `2015-04-01`, incluse dans l’URL ci-dessus.  |
| $filter | Option de filtrage pour réduire le jeu de résultats retournés. Les modèles autorisés pour ce paramètre sont disponibles [dans les informations de référence sur les journaux d’activité](/rest/api/monitor/activitylogs/list#uri-parameters). L’exemple montre la capture de tous les événements dans l’intervalle de temps 2018-05-16/2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corps de la demande

Aucun corps de requête n’est nécessaire pour cette opération.

## <a name="handle-the-response"></a>Gérer la réponse

Le code d’état 200 est retourné avec une liste des valeurs d’événement d’intégrité correspondant au paramètre de filtre, et avec un URI `nextlink` permettant de récupérer la page suivante de résultats.

## <a name="example-response"></a>Exemple de réponse 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
