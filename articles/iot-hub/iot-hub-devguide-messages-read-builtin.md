---
title: Comprendre le point de terminaison intégré Azure IoT Hub | Microsoft Docs
description: 'Guide du développeur : décrit comment utiliser le point de terminaison intégré et compatible avec Event Hub pour lire des messages appareil-à-cloud.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: e43d00fde0f76efa4398865757c44d94592b8291
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585507"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Lire des messages appareil-à-cloud à partir du point de terminaison intégré

Par défaut, les messages sont acheminés vers le point de terminaison côté service intégré (**messages/events**) compatible avec [Event Hubs](http://azure.microsoft.com/documentation/services/event-hubs/
). Ce point de terminaison est actuellement uniquement exposé à l’aide du protocole [AMQP](https://www.amqp.org/) sur le port 5671. Un IoT Hub expose les propriétés suivantes pour vous permettre de contrôler le point de terminaison de messages compatible avec Event Hub **messages/events** prédéfini.

| Propriété            | Description |
| ------------------- | ----------- |
| **Nombre de partitions** | Configurez cette propriété lors de la création pour définir le nombre de [partitions](../event-hubs/event-hubs-features.md#partitions) pour la réception d’événements appareil-à-cloud. |
| **Durée de rétention**  | Cette propriété spécifie la durée en jours de conservation des messages par IoT Hub. La durée par défaut est de un jour, mais elle peut être augmentée à sept jours. |

IoT Hub vous permet aussi de gérer des groupes de consommateurs sur le point de terminaison prédéfini de réception appareil vers cloud.

Si vous utilisez [le routage des messages](iot-hub-devguide-messages-d2c.md) et que la [route de secours](iot-hub-devguide-messages-d2c.md#fallback-route) est activée, tous les messages qui ne correspondent pas à une requête sur aucune route sont écrits sur le point de terminaison intégré. Si vous désactivez cette route de secours, les messages qui ne correspondent à aucune requête sont supprimés.

La durée de rétention peut être modifiée par programme, avec les [API REST de fournisseur de ressources IoT Hub](/rest/api/iothub/iothubresource) ou avec le [Portail Azure](https://portal.azure.com).

IoT Hub expose le point de terminaison prédéfini **messages/events** pour permettre à vos services principaux de lire les messages appareil vers cloud que reçoit votre hub. Ce point de terminaison est compatible avec Event Hub, ce qui vous permet d’utiliser tout mécanisme pris en charge par le service Event Hubs pour la lecture des messages.

## <a name="read-from-the-built-in-endpoint"></a>Lire à partir du point de terminaison intégré

Lorsque vous utilisez le [Kit de développement logiciel (SDK) Azure Service Bus pour .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) ou l’[hôte du processeur d’événements Event Hubs](..//event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md), vous pouvez utiliser toutes les chaînes de connexion IoT Hub avec les autorisations appropriées. Vous utilisez ensuite **messages/événements** comme nom d’Event Hub.

Si vous utilisez des Kits SDK (ou des intégrations de produits) qui ne sont pas reliés à IoT Hub, vous devrez récupérer un point de terminaison et un nom compatibles Event Hub :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

2. Cliquez sur **Points de terminaison intégrés**.

3. La section **Événements** contient les valeurs suivantes : **Point de terminaison compatible avec Event Hub**, **Nom compatible avec Event Hub**, **Partitions**, **Durée de conservation** et **Groupes de consommateurs**.

    ![Paramètres Appareil vers cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Le kit SDK IoT Hub requiert le nom de point de terminaison IoT Hub, à savoir **messages/events** comme indiqué sous **Points de terminaison**.

Si le SDK que vous utilisez requiert une valeur **Nom d’hôte** ou **Espace de noms**, supprimez le schéma du **Point de terminaison compatible avec Event Hub**. Par exemple, si votre point de terminaison compatible avec Event Hub est **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, le **Nom d’hôte** est **iothub-ns-myiothub-1234.servicebus.windows.net**. L’**Espace de noms** est **iothub-ns-myiothub-1234**.

Vous pouvez ensuite utiliser n’importe quelle stratégie d’accès partagé bénéficiant d’autorisations **ServiceConnect** pour vous connecter au Event Hub ci-dessus.

Si vous devez générer une chaîne de connexion Event Hub en utilisant les informations ci-dessus, vous pouvez utiliser le modèle suivant :

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Les kits SDK et intégrations que vous pouvez utiliser avec les points de terminaison compatibles avec Event Hub exposés par IoT Hub comprennent les éléments de la liste suivante :

* [Client Event Hubs Java](https://github.com/Azure/azure-event-hubs-java).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Vous pouvez afficher la [source spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) sur GitHub.
* [Intégration Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les points de terminaison IoT Hub, consultez [Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md).

* Les [guides de démarrage rapide](quickstart-send-telemetry-node.md) vous montrent comment envoyer des messages appareil-à-cloud à partir d’appareils simulés et lire les messages à partir du point de terminaison intégré. 

Pour plus de détails, consultez le didacticiel [Traiter les messages appareil-à-cloud IoT Hub en utilisant les itinéraires](tutorial-routing.md).

* Si vous voulez acheminer vos messages appareil-à-cloud vers des points de terminaison personnalisés, consultez [Utiliser des itinéraires de messages et des points de terminaison personnalisés pour les messages appareil-à-cloud](iot-hub-devguide-messages-read-custom.md).