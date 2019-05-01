---
title: Récupérer les mesures avec l’API REST
titlesuffix: Azure Load Balancer
description: Utilisez les API REST Azure pour collecter des métriques sur l’intégrité et l’utilisation de l’équilibreur de charge sur une plage de dates et une plage horaire définies.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 9f5206ef5348ee8fd7b3fe981a9cfe4afc1367fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734539"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Récupérer les métriques Azure Load Balancer avec l’API REST

Cette procédure montre comment collecter le nombre d’octets traités par [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) pour un intervalle de temps à l’aide de l’[API REST Azure](/rest/api/azure/).

Pour consulter la documentation de référence complète et d’autres exemples avec l’API REST, reportez-vous à [Informations de référence sur l’API REST Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Générer la demande

Utilisez la requête GET suivante pour collecter la métrique [ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) depuis Standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>En-têtes de requête

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Requis. Défini sur `application/json`.|  
|*Authorization :*|Requis. Défini sur un [jeton d’accès](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valide. |  

### <a name="uri-parameters"></a>Paramètres URI

| Nom | Description |
| :--- | :---------- |
| subscriptionId | L’ID d’abonnement d’un abonnement Azure. Si vous avez plusieurs abonnements, consultez [Utilisation de plusieurs abonnements](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| nom_groupe_ressources | Nom du groupe de ressources qui contient la ressource. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager, de CLI ou du portail. |
| loadBalancerName | Nom de l’équilibreur de charge Azure Load Balancer. |
| metricnames | Liste des [métriques Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics) valides, séparées par des virgules. |
| api-version | Version de l’API à utiliser pour la requête.<br /><br /> Ce document aborde la version d’API `2018-01-01`, incluse dans l’URL ci-dessus.  |
| intervalle de temps | Intervalle de temps de la requête. Il s’agit d’une chaîne au format `startDateTime_ISO/endDateTime_ISO`. Ce paramètre facultatif est défini pour retourner une journée de données dans l’exemple. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corps de la demande

Aucun corps de requête n’est nécessaire pour cette opération.

## <a name="handle-the-response"></a>Gérer la réponse

Le code d’état 200 est retourné lorsque la liste des valeurs de métriques est retournée. La liste complète des codes d’erreur est disponible dans la [documentation de référence](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Exemple de réponse 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
