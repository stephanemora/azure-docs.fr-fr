---
title: Réaction aux événements de clé-valeur de Configuration de l’application Azure | Microsoft Docs
description: Utilisez Azure Event Grid pour vous abonner aux événements de Configuration de l’application.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735645"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Réaction aux événements de Configuration de l’application Azure

Les événements de Configuration de l’application Azure permettent aux applications de réagir aux modifications de valeurs de clé. Pour cela, sans avoir besoin de code complexe ou services d’interrogation coûteux et inefficaces. Au lieu de cela, les événements sont envoyés via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) aux abonnés, comme [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou même à votre propre écouteur http personnalisé, et vous payez seulement pour ce que vous utilisez.

Les événements de Configuration de l’application Azure sont envoyés à Azure Event Grid, qui fournit des services de remise fiable à vos applications via enrichi de nouvelles tentatives stratégies et remise de lettres mortes. Pour plus d’informations, consultez [remise des messages Event Grid et nouvelle tentative](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Scénarios d’événements de configuration application courants incluent l’actualisation de la configuration d’application, déclencher des déploiements, ou n’importe quel flux de travail orienté sur la configuration. Lorsque les modifications sont peu fréquentes, mais que votre scénario requiert une réactivité immédiate, une architecture basée sur des événements peut être particulièrement efficace.

Jetez un coup de œil à [point de terminaison - CLI de web des événements de Configuration de l’application Azure Route personnalisée](./howto-app-configuration-event.md) pour obtenir un exemple rapide. 

![Modèle de Event Grid](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Événements de Configuration de l’application Azure disponibles
Event Grid utilise les [abonnements aux événements](../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés. Abonnements d’événements de Configuration des applications Azure peuvent inclure deux types d’événements :  

> |Nom de l'événement|Description|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Déclenché lorsqu’une valeur de clé est créée ou remplacée|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Déclenché lorsqu’une valeur de clé est supprimée.|

## <a name="event-schema"></a>Schéma d’événement
Les événements de Configuration de l’application Azure contiennent toutes les informations que vous avez besoin pour répondre aux modifications dans vos données. Vous pouvez identifier un événement de configuration d’application, car la propriété eventType commence par « Microsoft.AppConfiguration ». Plus d’informations sur l’utilisation des propriétés d’événement Event Grid sont documentées dans [schéma d’événement Event Grid](../event-grid/event-schema.md).  

> |Propriété|Type|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Id Azure Resource Manager complet de la configuration de l’application qui émet l’événement.|
> |subject|string|L’URI de la valeur de clé qui fait l’objet de l’événement.|
> |eventTime|string|Date/heure à laquelle l’événement a été généré, au format ISO 8601.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" or "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Id|string|Identificateur unique de cet événement.|
> |dataVersion|string|Version du schéma de l’objet de données.|
> |metadataVersion|string|Version du schéma des propriétés de niveau supérieur.|
> |data|objet|Collecte des données d’événement spécifique de Configuration de l’application Azure|
> |data.key|string|La clé de la valeur de clé qui a été modifiée ou supprimée.|
> |data.label|string|L’étiquette, le cas échéant, de la valeur de clé qui a été modifiée ou supprimée.|
> |data.etag|string|Pour `KeyValueModified` l’etag de la nouvelle valeur de clé. Pour `KeyValueDeleted` l’etag de la valeur de clé qui a été supprimée.|

Voici un exemple d’un événement KeyValueModified :
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Pour plus d’informations, consultez [schéma d’événements de Configuration de l’application Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Pratiques pour la consommation d’événements
Les applications qui gèrent des événements de configuration d’application doivent suivre certaines pratiques recommandées :
> [!div class="checklist"]
> * Comme plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements, il est important de ne pas supposer événements proviennent d’une source particulière, mais de vérifier le sujet du message pour vous assurer qu’il s’agit de la configuration de l’application que vous attendez.
> * De même, vérifiez que vous êtes prêt à traiter son eventType, et ne supposez pas que tous les événements reçus seront aux types que vous attendez.
> * Les messages pouvant arriver en désordre et après un certain temps, utilisez les champs etag pour comprendre si vos informations sur les objets sont toujours à jour.  En outre, utilisez les champs de séquence pour comprendre l’ordre des événements sur un objet particulier.
> * Le champ objet permet d’accéder à la valeur de clé qui a été modifiée.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Event Grid et essayer les événements de Configuration de l’application Azure :

- [À propos d’Event Grid](../event-grid/overview.md)
- [Acheminer des événements de Configuration de l’application Azure vers un point de terminaison web personnalisé](./howto-app-configuration-event.md)