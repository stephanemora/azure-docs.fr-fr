---
title: Gérer les points de terminaison et les itinéraires
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer et gérer les points de terminaison et les itinéraires d’événements pour les données d’Azure Digital Twins.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 923ae652872246916b2a4c5e8be95871983dbe95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559840"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Gérer les points de terminaison et les itinéraires dans Azure Digital Twins

Dans Azure Digital Twins, vous pouvez acheminer les [notifications d’événements](how-to-interpret-event-data.md) vers des services en aval ou des ressources de calcul. Pour ce faire, vous devez commencer par configurer des **points de terminaison** pouvant recevoir les événements, puis les [**itinéraires d’événements**](concepts-route-events.md) qui spécifient quels événements générés par Azure Digital Twins sont remis à quels points de terminaison.

Les types de points de terminaison pris en charge sont les suivants :
* [Concentrateur d’événements](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Pour plus d’informations sur les différents points de terminaison, consultez [Choisir entre les différents services de messagerie Azure](https://docs.microsoft.com/azure/event-grid/compare-messaging-services).

Vous pouvez gérer les points de terminaison et les itinéraires à l’aide des [**API EventRoutes**](how-to-use-apis-sdks.md), du [Kit de développement logiciel (SDK) C# .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) ou de l’[interface de ligne de commande Azure Digital Twins](how-to-use-cli.md). Vous pouvez également les gérer via le [portail Azure](https://portal.azure.com).

> [!NOTE]
> La gestion des itinéraires d’événements via le portail Azure n’est actuellement disponible que pour des utilisateurs Azure sur des comptes de domaine d’entreprise. 
>
>Si vous utilisez un [compte Microsoft (MSA)](https://account.microsoft.com/account/Account) personnel, tel un compte @outlook.com, utilisez les API ou l’interface de ligne de commande d’Azure Digital Twins pour gérer les itinéraires d’événements, comme décrit dans cet article.

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Créer un point de terminaison pour Azure Digital Twins

Pour lier un point de terminaison à Azure Digital Twins, l’Event Hub, une rubrique Event Grid ou le Service Bus que vous utilisez pour le point de terminaison doivent pré-exister. 

L’exemple suivant montre comment créer une rubrique Event Grid à l’aide d’Azure CLI :

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Pour obtenir la liste des noms de régions Azure qui peuvent être transmis à des commandes dans Azure CLI, exécutez la commande suivante :
> ```azurecli
> az account list-locations -o table
> ```

Une fois que vous avez créé la rubrique, vous pouvez la lier à Azure Digital Twins avec la commande suivante :

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

La rubrique Event Grid est ensuite disponible dans Azure Digital Twins, sous le nom spécifié avec l’argument `--endpoint-name`. Vous utiliserez généralement ce nom en tant que cible d’un **itinéraire d’événement** que vous allez créer dans la section suivante à l’aide de l’API de service Azure Digital Twins.

Des commandes équivalentes existent pour les points de terminaison Event Hub et Service Bus :

* Ajouter un point de terminaison de rubrique Service Bus (nécessite une ressource Service Bus créée au préalable)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Ajouter un point de terminaison Event Hub (nécessite une ressource Service Bus créée au préalable)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Gérer les itinéraires d’événements avec des API

Pour envoyer concrètement des données d’Azure Digital Twins à un point de terminaison, vous devez définir un itinéraire d’événement. Les **API EventRoutes** d’Azure Digital Twins permettent aux développeurs de lier le flux d’événements au sein du système et aux services en aval. Pour en savoir plus sur les itinéraires d’événements, consultez [Concepts : Routage des événements Azure Digital Twins](concepts-route-events.md).

Les exemples fournis dans cet article utilisent le Kit de développement logiciel (SDK) C#.

Les itinéraires d’événements sont définis à l’aide d’API de plan de données. Une définition d’itinéraire peut contenir les éléments suivants :
* ID de l’itinéraire que vous souhaitez utiliser ;
* nom du point de terminaison que vous souhaitez utiliser ;
* filtre définissant les événements envoyés au point de terminaison. 

À défaut d’ID d’itinéraire, aucun message n’est acheminé en dehors d’Azure Digital Twins. S’il existe un ID d’itinéraire et que le filtre est `true`, tous les messages sont routés vers le point de terminaison. Si un ID d’itinéraire et un filtre différent sont ajoutés, les messages sont filtrés en fonction du filtre.

Un itinéraire doit permettre la sélection de plusieurs notifications et types d’événements. 

`CreateEventRoute` est l’appel du kit de développement logiciel (SDK) utilisé pour ajouter un itinéraire d’événement. Voici un exemple de son utilisation :

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Toutes les fonctions du Kit de développement logiciel (SDK) sont disponibles en versions synchrone et asynchrone.

### <a name="event-route-sample-code"></a>Exemple de code d’itinéraire d’événement

L’exemple de code suivant montre comment créer, répertorier et supprimer un itinéraire d’événement :
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Filtrer les événements

Sans filtrage, les points de terminaison reçoivent un grand nombre d’événements d’Azure Digital Twins :
* télémétrie déclenchée par des [jumeaux numériques](concepts-twins-graph.md) à l’aide de l’API de service d’Azure Digital Twins ;
* notifications de changement de propriété de jumeau, déclenchées par des modifications des propriétés d’un jumeau dans l’instance Azure Digital Twins ;
* événements de cycle de vie déclenchés lors de la création ou de la suppression de jumeaux ou de relations ;
* événements de changement de modèle déclenchés lors de l’ajout ou de la suppression de [modèles](concepts-models.md) configurés dans une instance Azure Digital Twins.

Vous pouvez limiter les événements envoyés en ajoutant un filtre à un point de terminaison.

Pour ajouter un filtre, vous pouvez utiliser une demande PUT adressée à *https://{YourHost}/EventRoutes/myNewRoute?api-version=2020-05-31-preview* avec le corps suivant :

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Voici les filtres d’itinéraire pris en charge.

| Nom du filtre | Description | Schéma du filtre | Valeurs prises en charge | 
| --- | --- | --- | --- |
| Type | [Type d’événement](./concepts-route-events.md#types-of-event-messages) circulant dans votre instance de jumeau numérique. | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Source | Nom de l’instance Azure Digital Twins. | `"filter" : "source = '<hostname>'"`|  **Pour les notifications** : `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Pour la télémétrie** : `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Objet | Description de l’événement dans le contexte de la source de l’événement ci-dessus | `"filter": " subject = '<subject>'"` | **Pour les notifications** : l’objet est `<twinid>` <br> ou un format d’URI pour les objets identifiés de façon unique par plusieurs éléments ou ID :<br>`<twinid>/relationships/<relationshipid>`<br> **Pour la télémétrie** : l’objet est le chemin d’accès du composant (si la télémétrie est émise à partir d’un composant jumeau), par exemple `comp1.comp2`. Si la télémétrie n’est pas émise à partir d’un composant, le champ d’objet est vide. |
| Schéma de données | ID de modèle DTDL | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Pour la télémétrie** : le schéma de données est l’ID de modèle du jumeau ou du composant qui émet la télémétrie. <br>**Pour les notifications** : le schéma de données n’est pas pris en charge.|
| Type de contenu | Type de contenu de la valeur de données | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Version de spécification | Version du schéma d’événement que vous utilisez | `"filter": "specversion = '<version>'"` | Doit être `1.0`. Cela indique le schéma CloudEvents version 1.0. |
| True / False | Autorise la création d’un itinéraire sans filtrage ou la désactivation d’un itinéraire | `"filter" : "<true/false>"` | `true` = l’itinéraire est activé sans filtrage. <br> `false` = l’itinéraire est désactivé |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Il est également possible de combiner des filtres à l’aide des opérations suivantes :

| Nom du filtre | Schéma du filtre | Exemple | 
| --- | --- | --- |
| AND / OR | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| AND / OR | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Opérations imbriquées | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Pendant la période de préversion, seule l’égalité de chaîne est prise en charge (=, ! =).

Lorsque vous implémentez ou mettez à jour un filtre, quelques minutes peuvent s’écouler avant que la modification apparaisse dans le pipeline de données.

## <a name="manage-endpoints-and-routes-with-cli"></a>Gérer les points de terminaison et les itinéraires avec l’interface de ligne de commande

Vous pouvez également gérer les points de terminaison et les itinéraires à l’aide de l’interface de commande d’Azure Digital Twins. Les commandes sont énumérées dans [Guide pratique pour utiliser l’interface de ligne de commande d’Azure Digital Twins](how-to-use-cli.md)

## <a name="monitor-event-routes"></a>Surveiller les itinéraires d’événements

Vous pouvez consulter les métriques de routage telles que le nombre, la latence et le taux d’échec dans le [portail Azure](https://portal.azure.com/). 

Dans la page d’accueil du portail, recherchez votre instance Azure Digital Twins pour en extraire les détails. Sélectionnez l’option **Métriques** dans le menu de l’instance Azure Digital Twins pour afficher la page *Métriques*.

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Page métriques d’une instance Azure Digital Twins dans le portail Azure":::

À partir de là, vous pouvez afficher les métriques de votre instance et créer des affichages personnalisés.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les différents types de messages d’événements que vous pouvez recevoir :
* [Guide pratique pour interpréter des données d’événement](how-to-interpret-event-data.md)
