---
title: Réagir aux événements de carte avec Event Grid | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à réagir aux événements Microsoft Azure Maps avec Event Grid.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9a946d189706c9c789ab884670d13b0b3e7fcb0c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911807"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Réagir aux événements Azure Maps à l'aide d'Event Grid 

Azure Maps s'intègre à Azure Event Grid pour vous permettre d'envoyer des notifications d'événements à d'autres services et de déclencher des processus en aval. Le but de cet article est de vous aider à configurer vos applications métier pour écouter les événements Azure Maps afin de pouvoir réagir aux événements critiques de manière fiable, évolutive et sécurisée. Vous pouvez par exemple créer une application pour effectuer plusieurs actions telles que la mise à jour d'une base de données, la création d'un ticket et la remise d'une notification par e-mail chaque fois qu'un appareil franchit une limite géographique.

Azure Event Grid est un service de routage d'événement complètement managé qui utilise le modèle publication-abonnement. Event Grid offre une prise en charge intégrée des services Azure comme [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) et [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview), et peut fournir des alertes d’événements à des services non-Azure à l’aide de webhooks. Pour obtenir une liste complète des gestionnaires d’événements qui prennent en charge Event Grid, consultez [Présentation d’Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Modèle Azure Event Grid fonctionnel](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Types d'événements Azure Maps

Event Grid utilise les [abonnements aux événements](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) pour acheminer les messages d’événements vers les abonnés. Un compte Azure Maps émet les types d'événements suivants : 

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Déclenché lorsque les coordonnées reçues sont passées de l’extérieur d’une limite géographique donnée à l’intérieur d’une zone donnée. |
| Microsoft.Maps.GeofenceExited | Déclenché lorsque les coordonnées reçues sont passées de l’intérieur d’une limite géographique donnée à l’extérieur de celle-ci. |
| Microsoft.Maps.GeofenceResult | Déclenché chaque fois qu’une requête de geofencing renvoie un résultat, quel que soit l’état. |

## <a name="event-schema"></a>Schéma d’événement

L'exemple suivant présente le schéma de GeofenceResult.

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>Conseils relatifs à la consommation d’événements

Les applications qui gèrent des événements de limite géographique Azure Maps doivent suivre certaines pratiques recommandées :

* Plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d'événements. Il est important de ne pas présumer que les événements proviennent d'une source particulière. Vérifiez toujours le sujet du message pour vous assurer qu'il provient bien de la source attendue.
* Les messages peuvent arriver dans le désordre ou après un certain délai. Utilisez le champ `X-Correlation-id` de l'en-tête de la réponse pour savoir si vos informations sur les objets sont à jour.
* Lorsque les API de limite géographique Get et POST sont appelées avec le paramètre de mode défini sur `EnterAndExit`, un événement d'entrée ou de sortie est généré pour chaque géométrie de la limite géographique dont l'état a changé par rapport à l'appel précédent de l'API de limite géographique.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l'utilisation du geofencing afin de contrôler les opérations sur un site de construction, consultez :

> [!div class="nextstepaction"] 
> [Configurer une limite géographique à l'aide d'Azure Maps](tutorial-geofence.md)