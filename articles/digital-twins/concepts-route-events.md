---
title: Routes d’événements
titleSuffix: Azure Digital Twins
description: Découvrez comment acheminer des événements dans Azure Digital Twins et vers d’autres services Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7c1f0c18fc8b867e16ce539a24839637e1d14379
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390786"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Acheminer des événements à l’intérieur et à l’extérieur d’Azure Digital Twins

Azure Digital Twins utilise des **routes d’événements** pour envoyer des données aux consommateurs en dehors du service. 

Avec la préversion, il existe deux cas principaux d’envoi de données Azure Digital Twins :
* Envoi de données d’une représentation dans le graphique Azure Digital Twins vers une autre. Par exemple, lorsqu’une propriété au sein d’une représentation numérique change, vous souhaiterez peut-être notifier et mettre à jour une autre représentation numérique.
* Envoi de données à des services de données en aval pour un stockage ou un traitement supplémentaire (également appelé *sortie de données*). Par exemple,
  - Un hôpital peut vouloir envoyer des données d’événements Azure Digital Twins vers [Time Series Insights (TSI)](../time-series-insights/time-series-insights-update-overview.md), pour enregistrer des données de série chronologique relatives à des événements liés au lavage de mains pour une analyse en masse.
  - Une entreprise qui utilise déjà [Azure Maps](../azure-maps/about-azure-maps.md) souhaite utiliser Azure Digital Twins pour améliorer sa solution. Ils peuvent rapidement activer une carte Azure après avoir configuré Azure Digital Twins, amener des entités Azure Maps dans Azure Digital Twins en tant que [représentations numériques](concepts-twins-graph.md) dans le graphique de représentation, ou exécuter des requêtes puissantes en tirant parti des données de Azure Maps et Azure Digital Twins.

Les routes d’événements sont utilisées pour ces deux scénarios.

## <a name="about-event-routes"></a>À propos des routes d’événements

Une route d’événement vous permet d’envoyer des données d’événements à partir de représentations numériques dans Azure Digital Twins vers des points de terminaison personnalisés au sein de vos abonnements. Trois services Azure sont actuellement pris en charge pour les points de terminaison : [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md) et [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Chacun de ces services Azure peut être connecté à d’autres services et agit en tant qu’intermédiaire, en envoyant des données aux destinations finales, comme TSI ou Azure Maps, quel que soit le traitement dont vous avez besoin.

Le diagramme suivant illustre le flux des données d’événement via une solution IoT plus grande avec un aspect Azure Digital Twins :

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Acheminement des données de Azure Digital Twins via des points de terminaison vers plusieurs services en aval" border="false":::

Les cibles en aval classiques pour les routes d’événements sont des ressources comme TSI, Azure Maps, le stockage et les solutions d’analyse.

### <a name="event-routes-for-internal-digital-twin-events"></a>Routes d’événements pour les événements internes de représentation numérique

Au cours de la préversion actuelle, les routes d’événements sont également utilisées pour gérer les événements dans le graphique de représentation et envoyer des données entre des représentations numériques. Pour ce faire, vous pouvez connecter des routes d’événements aux ressources de calcul via Event Grid, par exemple [Azure Functions](../azure-functions/functions-overview.md). Ces fonctions définissent ensuite la façon dont les représentations reçoivent les événements et y répondent. 

Lorsqu’une ressource de calcul souhaite modifier le graphique de représentation en fonction d’un événement reçu par un itinéraire d’événement, il peut être utile de connaître à l’avance le type de représentation qu’elle souhaite modifier. 

Le message d’événement contient également l’ID de la représentation source ayant envoyé le message. La ressource de calcul peut donc utiliser des requêtes ou parcourir les relations pour trouver une représentation cible pour l’opération souhaitée. 

La ressource de calcul doit également établir des autorisations de sécurité et d’accès de façon indépendante.

Pour passer en revue le processus de configuration d’une fonction Azure pour traiter les événements de représentation numérique, consultez [Guide pratique pour : Configurer une fonction Azure pour le traitement des données](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Créer un point de terminaison

Pour définir une route d’événement, les développeurs doivent d’abord définir des points de terminaison. Un **point de terminaison** est une destination en dehors d’Azure Digital Twins qui prend en charge une connexion d’itinéraire. Les destinations prises en charge dans la préversion actuelle sont :
* Rubriques personnalisées Event Grid
* Event Hub
* Service Bus

Les points de terminaison sont configurés à l’aide d’API de plan de contrôle (prises en charge par [l’interface CLI Azure Digital Twins](how-to-use-cli.md) ou via le portail Azure. Une définition de point de terminaison donne :
* Le nom du point de terminaison
* Le type de point de terminaison (Event Grid, Event Hub ou Service Bus)
* Les chaînes de connexion primaire et secondaire pour l’authentification 
* Le chemin d’accès de rubrique du point de terminaison, par exemple *your-topic.westus2.eventgrid.azure.net*

Les API de point de terminaison disponibles dans le plan de contrôle sont les suivantes :
* Création d’un point de terminaison
* Obtention de la liste des points de terminaison
* Obtention d’un point de terminaison par son nom
* Suppression d’un point de terminaison par son nom

## <a name="create-an-event-route"></a>Création d’un itinéraire d’événements
 
Les routes d’événements sont créées dans une application cliente avec l’appel de [kit de développement logiciel .NET (C#) ](how-to-use-apis-sdks.md) : 

```csharp
await client.EventRoutes.AddAsync("<name-for-the-new-route>", new EventRoute("<endpoint-name>"));
```

* Le `endpoint-name` identifie un point de terminaison, comme Event Hub, Event Grid ou Service Bus. Ces points de terminaison doivent être créés dans votre abonnement et attachés à Azure Digital Twins à l’aide des API de plan de contrôle avant d’effectuer cet appel d’inscription.

L’objet de l’itinéraire d’événement passé à `EventRoutes.Add` prend également un paramètre [**filtre**](./how-to-manage-routes.md#filter-events), utilisable pour restreindre les types d’événements qui suivent cet itinéraire.

Les routes peuvent également être créées à l’aide de [l’interface CLI Azure Digital Twins](how-to-use-cli.md).

### <a name="types-of-event-messages"></a>Types de messages d'événements

Les différents types d’événements dans IoT Hub et Azure Digital Twins produisent différents types de messages de notification, comme décrit ci-dessous.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer et gérer un itinéraire d’événement :
* [Guide pratique pour Gérer les points de terminaison et les routes](how-to-manage-routes.md)

Ou bien, découvrez comment utiliser Azure Functions pour acheminer des événements dans Azure Digital Twins :
* [Guide pratique pour Configurer une fonction Azure pour le traitement des données](how-to-create-azure-function.md)