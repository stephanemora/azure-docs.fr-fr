---
title: Azure Event Grid – Définir des en-têtes personnalisés sur des événements remis
description: Décrit comment vous pouvez définir des en-têtes personnalisés (ou des propriétés de remise) sur les événements remis.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: fb6f0de7919ed7cf9072c0fa35e8f9be5cb5e7db
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278284"
---
# <a name="custom-delivery-properties"></a>Propriétés de remise personnalisées
Les abonnements aux événements vous permettent de définir des en-têtes HTTP qui sont inclus dans les événements remis. Cette fonctionnalité vous permet de définir des en-têtes personnalisés requis par une destination. Vous pouvez configurer jusqu’à 10 en-têtes lors de la création d’un abonnement aux événements. La valeur de chaque en-tête ne doit pas être supérieure à 4 096 (4 Ko) octets.

Vous pouvez définir des en-têtes personnalisés sur les événements remis aux destinations suivantes :

- webhooks
- Rubriques et files d’attente Azure Service Bus
- Hubs d'événements Azure
- Connexions hybrides Relay

Lorsque vous créez un abonnement aux événements dans le portail Azure, vous pouvez utiliser l’onglet **Propriétés de remise** pour définir des en-têtes HTTP personnalisés. Cette page vous permet de définir des valeurs d’en-tête fixes et dynamiques.

## <a name="setting-static-header-values"></a>Définition de valeurs d’en-tête statique
Pour définir des en-têtes avec une valeur fixe, indiquez le nom de l’en-tête et sa valeur dans les champs correspondants :

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Propriétés de remise – Statique":::

Vous souhaiterez spécifier **Est secrète** lorsque vous fournissez des données sensibles. Les données sensibles ne seront pas affichées sur le portail Azure. 

## <a name="setting-dynamic-header-values"></a>Définition de valeurs d’en-tête dynamique
Vous pouvez définir la valeur d’un en-tête en fonction d’une propriété dans un événement entrant. Utilisez la syntaxe JsonPath pour faire référence à la valeur de propriété d’un événement entrant à utiliser comme valeur pour un en-tête dans des demandes sortantes. Par exemple, pour définir la valeur d’un en-tête nommé **Channel** à l’aide de la valeur du **système** de propriétés d’événement entrant dans les données d’événement, configurez votre abonnement aux événements comme suit :

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Propriétés de remise – Dynamique":::

## <a name="examples"></a>Exemples
Cette section fournit quelques exemples d’utilisation d’utilisation des propriétés de remise.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Définition de l’en-tête Authorization avec un jeton du porteur (exemple non normatif)

Définissez une valeur sur un en-tête Authorization pour identifier la demande auprès de votre gestionnaire de webhook. Vous pouvez définir un en-tête Authorization si vous [ne protégez pas votre webhook avec Azure Active Directory](secure-webhook-delivery.md).

| Nom de l’en-tête   | Type d’en-tête | Valeur de l’en-tête |
| :--           | :--         | :--            |
|`Authorization` | statique | `BEARER SlAV32hkKG...`|

Les demandes sortantes doivent maintenant contenir l’en-tête défini sur l’abonnement aux événements :

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> La définition d’en-têtes Authorization est une option raisonnable quand votre destination est un webhook. Il ne faut pas l’utiliser pour des [fonctions souscrites avec un ID de ressource](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs et Connexions hybrides, car ces destinations prennent en charge leurs propres schémas d’authentification quand elle sont utilisées avec Event Grid.

### <a name="service-bus-example"></a>Exemple Service Bus
Azure Service Bus prend en charge l’utilisation d’un [en-tête HTTP BrokerProperties](/rest/api/servicebus/message-headers-and-properties#message-headers) pour définir des propriétés de message lors de l’envoi de messages uniques. La valeur de l’en-tête `BrokerProperties` doit être fournie au format JSON. Par exemple, si vous devez définir des propriétés de message lors de l’envoi d’un message à Service Bus, définissez l’en-tête comme suit :

| Nom de l’en-tête | Type d’en-tête | Valeur de l’en-tête |
| :-- | :-- | :-- |
|`BrokerProperties` | statique     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Exemple Event Hubs

Si vous devez publier des événements dans une partition spécifique au sein d’un Event Hub, définissez un [en-tête HTTP BrokerProperties](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) sur votre abonnement aux événements pour spécifier la clé de partition qui identifie la partition Event Hub cible.

| Nom de l’en-tête | Type d’en-tête | Valeur de l’en-tête                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | statique | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Configurer la durée de vie des événements sortants vers les files d’attente de Stockage Azure
Pour la destination des files d’attente de Stockage Azure, vous pouvez uniquement configurer la durée de vie du message sortant, une fois celui-ci remis à une file d’attente de Stockage Azure. Si aucun temps n’est fourni, la durée de vie par défaut du message est de 7 jours. Vous pouvez également définir l’événement pour qu’il n’expire jamais.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Propriétés de remise – File d’attente de stockage":::

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la remise d’événements, consultez l’article suivant :

- [Livraison et nouvelle tentative](delivery-and-retry.md)
- [Remise d’événements WebHook](webhook-event-delivery.md)
- [Filtre d'événement](event-filtering.md)
