---
title: Routage des événements et des messages avec Azure Digital Twins | Microsoft Docs
description: Vue d’ensemble du routage des événements et des messages vers des points de terminaison de service avec Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323901"
---
# <a name="routing-events-and-messages"></a>Routage des événements et des messages

Les solutions IoT unifient souvent plusieurs services puissants, dont le stockage et l’analytique. Cet article explique comment connecter des applications Azure Digital Twins aux services de stockage, d’intelligence artificielle et d’analytique Azure pour les enrichir d’insights et de fonctionnalités avancés.

## <a name="route-types"></a>Types de routage

Digital Twins propose deux façons d’intégrer les événements IoT à d’autres applications métier ou services Azure :

* **Routage des événements Digital Twins** : les événements Azure Digital Twins peuvent être déclenchés quand un objet du graphe spatial change, quand des données de télémétrie sont reçues ou quand une fonction définie par l’utilisateur crée une notification en fonction de conditions prédéfinies. Les utilisateurs peuvent envoyer ces événements à [Event Hubs](https://azure.microsoft.com/services/event-hubs/), à des [rubriques Service Bus](https://azure.microsoft.com/services/service-bus/) ou à [Event Grid](https://azure.microsoft.com/services/event-grid/) pour les traiter ultérieurement.

* **Routage des données de télémétrie d’appareil** : en plus de router les événements, Azure Digital Twins peut router les messages de télémétrie d’appareil bruts vers Event Hubs pour obtenir des insights et une analytique supplémentaires. Ces types de messages ne sont pas traités par Digital Twins ; ils sont uniquement transférés vers le **hub d’événements**.

Les utilisateurs peuvent spécifier un ou plusieurs points de sortie pour envoyer des événements ou transférer des messages. Les événements et les messages sont envoyés aux points de terminaison en fonction de ces préférences de routage prédéfinies. En d’autres termes, les utilisateurs peuvent spécifier un point de terminaison pour la réception des événements d’opération de graphe, un autre pour la réception des événements de télémétrie d’appareil, et ainsi de suite.

![Routage des événements Digital Twins][1]

Le routage vers **Event Hubs** conserve l’ordre dans lequel les messages de télémétrie sont envoyés ; ainsi, ces derniers arrivent au point de terminaison dans l’ordre dans lequel ils ont été initialement reçus. **Event Grid** et **Service Bus** ne garantissent pas que les points de terminaison recevront les événements dans l’ordre dans lequel ils se sont produits. Toutefois, le schéma d’événement comprend un horodatage que vous pouvez utiliser pour identifier l’ordre une fois les événements arrivés au point de terminaison.

## <a name="route-implementation"></a>Implémentation du routage

Le service Azure Digital Twins prend en charge les types de point de terminaison (**EndpointTypes**) suivants :

* **EventHub** : point de terminaison de chaîne de connexion Event Hub.
* **ServiceBus** : point de terminaison de chaîne de connexion Service Bus.
* **EventGrid** : point de terminaison de chaîne de connexion Event Grid.

Azure Digital Twins prend en charge les types d’événement (**EventTypes**) suivants qui sont envoyés au point de terminaison choisi :

* **DeviceMessages** : messages de télémétrie envoyés à partir des appareils des utilisateurs et transférés par le système.
* **TopologyOperation** : opérations qui changent le graphe ou ses métadonnées. Par exemple, ajout ou suppression d’une entité, telle qu’un espace.
* **SpaceChange** : modifications de la valeur calculée d’un espace à la suite d’un message de télémétrie d’appareil.
* **SensorChange** : modifications de la valeur calculée d’un capteur à la suite d’un message de télémétrie d’appareil.
* **UdfCustom** : notifications personnalisées issues d’une fonction définie par l’utilisateur.

> [!IMPORTANT]
> Les types de point de terminaison (**EndpointTypes**) ne prennent pas tous en charge tous les types d’événement (**EventTypes**).
> Le tableau ci-dessous indique les types d’événement (**EventTypes**) autorisés pour chaque type de point de terminaison (**EndpointType**).

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **ServiceBus**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>Pour obtenir plus d’informations sur la création de points de terminaison et des exemples de schéma d’événements, consultez [Sortie et points de terminaison](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les limites de la préversion publique, consultez [Limites de la préversion d’Azure Digital Twins](concepts-service-limits.md).

Pour tester un exemple Azure Digital Twins, consultez [Démarrage rapide : Rechercher des salles disponibles](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png