---
title: Azure Event Grid – Définir des en-têtes personnalisés sur des événements remis
description: Décrit comment vous pouvez définir des en-têtes personnalisés (ou des propriétés de remise) sur les événements remis.
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 3600d74d91ad218f3fcab99002762d605fba3139
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831349"
---
# <a name="custom-delivery-properties"></a>Propriétés de remise personnalisées
Les abonnements aux événements vous permettent de définir des en-têtes HTTP qui sont inclus dans les événements remis. Cette fonctionnalité vous permet de définir des en-têtes personnalisés requis par une destination. Vous pouvez configurer jusqu’à 10 en-têtes lors de la création d’un abonnement aux événements. La valeur de chaque en-tête ne doit pas être supérieure à 4 096 (4 Ko) octets.

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

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Utilisez le paramètre `--delivery-attribute-mapping` lors de la création d’un abonnement à l’aide la commande `az eventgrid event-subscription create`. Voici un exemple :

```azurecli
az eventgrid event-subscription create -n es1 \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1
    --endpoint-type storagequeue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/sa1/queueservices/default/queues/q1 \
    --enable-advanced-filtering-on-arrays true
    --delivery-attribute-mapping staticproperty1 static somestaticvalue2 true 
    --delivery-attribute-mapping staticproperty2 static somestaticvalue3 false 
    --delivery-attribute-mapping dynamicproperty1 dynamic data.key1
```

## <a name="examples"></a>Exemples
Cette section fournit quelques exemples d’utilisation d’utilisation des propriétés de remise.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Définition de l’en-tête Authorization avec un jeton du porteur (exemple non normatif)

Définissez une valeur sur un en-tête Authorization pour identifier la demande auprès de votre gestionnaire de webhook. Vous pouvez définir un en-tête Authorization si vous [ne protégez pas votre webhook avec Azure Active Directory](secure-webhook-delivery.md).

| Nom de l’en-tête   | Type d’en-tête | Valeur de l’en-tête |
| :--           | :--         | :--            |
|`Authorization` | statique | `BEARER SlAV32hkKG...`|

Les demandes sortantes doivent maintenant contenir l’en-tête défini sur l’abonnement aux événements :

```console
POST /home.html HTTP/1.1
Host: acme.com

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> La définition d’en-têtes Authorization est une option raisonnable quand votre destination est un webhook. Il ne faut pas l’utiliser pour des [fonctions souscrites avec un ID de ressource](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs et Connexions hybrides, car ces destinations prennent en charge leurs propres schémas d’authentification quand elle sont utilisées avec Event Grid.

### <a name="service-bus-example"></a>Exemple Service Bus
Azure Service Bus prend en charge l’utilisation des propriétés de message suivantes lors de l’envoi de messages uniques. 

| Nom de l’en-tête | Type d’en-tête |
| :-- | :-- |
| `MessageId` | dynamique |  
| `PartitionKey` | Statique ou dynamique |
| `SessionId` | Statique ou dynamique |
| `CorrelationId` | Statique ou dynamique |
| `Label` | Statique ou dynamique |
| `ReplyTo` | Statique ou dynamique | 
| `ReplyToSessionId` | Statique ou dynamique |
| `To` |Statique ou dynamique |
| `ViaPartitionKey` | Statique ou dynamique |

> [!NOTE]
> - La valeur par défaut de `MessageId` est l’ID interne de l’événement Event Grid. Vous pouvez la remplacer. Par exemple : `data.field`.
> - Vous pouvez définir uniquement `SessionId` ou `MessageId`. 

### <a name="event-hubs-example"></a>Exemple Event Hubs

Si vous devez publier des événements dans une partition spécifique au sein d’un Event Hub, définissez une propriété `ParitionKey` sur votre abonnement aux événements pour spécifier la clé de partition qui identifie la partition Event Hub cible.

| Nom de l’en-tête | Type d’en-tête |
| :-- | :-- |
|`PartitionKey` | statique |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Configurer la durée de vie des événements sortants vers les files d’attente de Stockage Azure
Pour la destination des files d’attente de Stockage Azure, vous pouvez uniquement configurer la durée de vie du message sortant, une fois celui-ci remis à une file d’attente de Stockage Azure. Si aucun temps n’est fourni, la durée de vie par défaut du message est de 7 jours. Vous pouvez également définir l’événement pour qu’il n’expire jamais.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Propriétés de remise – File d’attente de stockage":::

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la remise d’événements, consultez l’article suivant :

- [Livraison et nouvelle tentative](delivery-and-retry.md)
- [Remise d’événements WebHook](webhook-event-delivery.md)
- [Filtre d'événement](event-filtering.md)
