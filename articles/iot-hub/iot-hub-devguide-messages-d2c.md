---
title: Comprendre le routage des messages IoT Hub | Microsoft Docs
description: 'Guide du développeur : Comment utiliser le routage de messages pour envoyer des messages appareil-à-cloud. Inclut des informations sur l’envoi de données de télémétrie et d’autres données.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: abc32b726eea55f08a052f29a12f1eb237d4f5d6
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311318"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Utiliser le routage des messages pour envoyer des messages appareil-à-cloud à différents points de terminaison

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Le routage des messages vous permet d’envoyer des messages de vos appareils vers des services cloud de façon automatisée, évolutive et fiable. Le routage des messages peut être utilisé pour : 

* **L’envoi de messages de télémétrie des appareils ainsi que des événements**, c’est-à-dire les événements de cycle de vie des appareils et les événements de modification des jumeaux d’appareil aux points de terminaison intégrés et aux de points de terminaison personnalisés. Découvrez plus d’informations sur les [points de terminaison de routage](#routing-endpoints).

* **Le filtrage des données avant leur routage vers différents points de terminaison** en appliquant des requêtes avancées. Le routage de messages vous permet d’interroger les propriétés et le corps d’un message, ainsi que les étiquettes et les propriétés d’un jumeau d’appareil. Découvrez plus d’informations sur l’utilisation de [requêtes dans le routage des messages](iot-hub-devguide-routing-query-syntax.md).

IoT Hub doit pouvoir accéder en écriture à ces points de terminaison de service pour que le routage des messages fonctionne. Si vous configurez vos points de terminaison via le portail Azure, les autorisations nécessaires sont ajoutées pour vous. Veillez à configurer vos services pour prendre en charge le débit prévu. Lorsque vous configurez votre solution IoT pour la première fois, vous devrez peut-être surveiller vos points de terminaison supplémentaires et apporter les modifications nécessaires en fonction de la charge réelle.

IoT Hub définit un [format commun](iot-hub-devguide-messages-construct.md) pour toute messagerie appareil-à-cloud, afin de favoriser l’interopérabilité entre les différents protocoles. Si un message correspond à plusieurs routes qui pointent vers le même point de terminaison, IoT Hub ne remet ce message qu’une seule fois à ce point de terminaison. Par conséquent, vous n’avez pas besoin de configurer une déduplication sur votre file d’attente ou votre rubrique Service Bus. Dans les files d’attente partitionnées, l’affinité de la partition assure le classement des messages. Utilisez ce tutoriel pour découvrir comment [configurer le routage des messages] (tutorial-routing.md).

## <a name="routing-endpoints"></a>Points de terminaison de routage

Un hub IoT a un point de terminaison intégré par défaut (**messages/événements**) qui est compatible avec Event Hubs. Vous pouvez créer des [points de terminaison personnalisés](iot-hub-devguide-endpoints.md#custom-endpoints) pour y router les messages en liant d’autres services de votre abonnement au hub IoT. IoT Hub prend actuellement en charge les services suivants en tant que points de terminaison personnalisés :

### <a name="built-in-endpoint"></a>Point de terminaison intégré

Vous pouvez utiliser [l’intégration et les SDK standard Event Hubs](iot-hub-devguide-messages-read-builtin.md) pour recevoir des messages appareil-à-cloud du point de terminaison intégré (**messages/événements**). Notez qu’une fois qu’une route est créée, les données cessent de circuler vers le point de terminaison intégré, sauf si une route est créée vers ce point de terminaison.

### <a name="azure-blob-storage"></a>Stockage Blob Azure

IoT Hub prend en charge l’écriture de données sur Stockage Blob Azure seulement au format [Apache Avro](http://avro.apache.org/). IoT Hub regroupe les messages dans des lots et écrit les données dans un objet blob quand le lot atteint une certaine taille ou après un certain laps de temps.

IoT Hub utilise par défaut la convention d’affectation de noms de fichiers suivante :

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Vous pouvez utiliser n’importe quelle convention de nommage des fichiers, mais vous devez utiliser tous les jetons listés. IoT Hub écrit dans un objet blob vide s’il n’y a aucune donnée à écrire.

### <a name="service-bus-queues-and-service-bus-topics"></a>Files d’attente et rubriques Service Bus

Les options **Sessions** ou **Détection des doublons** ne doivent pas être activées pour les files d’attente et rubriques Service Bus utilisées comme points de terminaison IoT Hub. Si l’une de ces options est activée, le point de terminaison s’affiche comme **Inaccessible** dans le portail Azure.

### <a name="event-hubs"></a>Event Hubs

En plus du point de terminaison compatible Event Hubs intégré, vous pouvez router des données vers des points de terminaison personnalisés de type Event Hubs. 

Lorsque vous utilisez des points de terminaison de routage et personnalisés, les messages sont uniquement remis au point de terminaison intégré s’ils ne correspondent à aucune règle. Pour remettre des messages au point de terminaison intégré et à des points de terminaison personnalisés, ajoutez une route qui envoie les messages au point de terminaison des événements.

## <a name="reading-data-that-has-been-routed"></a>Lecture de données qui ont été routées

Vous pouvez configurer une route en suivant ce [tutoriel](tutorial-routing.md).

Utilisez les tutoriels suivants pour découvrir comment lire un message à partir d’un point de terminaison.

* Lecture à partir du [point de terminaison intégré](quickstart-send-telemetry-node.md)

* Lecture à partir du [Stockage Blob](../storage/blobs/storage-blob-event-quickstart.md)

* Lecture à partir [d’Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lecture à partir de [files d’attente Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lecture à partir de [rubriques Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Itinéraire de secours

La route de secours envoie tous les messages qui ne satisfont pas aux conditions de la requête sur une des routes existantes aux hubs d’événements existants (**messages/événements**), compatible avec [Event Hubs](/azure/event-hubs/). Si le routage des messages est activé, vous pouvez activer la fonctionnalité de route de secours. Notez qu’une fois qu’une route est créée, les données cessent de circuler vers le point de terminaison intégré, sauf si une route est créée vers ce point de terminaison. S’il n’existe pas de route vers le point de terminaison intégré et qu’une route de secours est activée, seuls les messages qui ne correspondent pas aux conditions de la requête sur les routes sont envoyées au point de terminaison intégré. En outre, si toutes les routes existantes sont supprimées, la route de secours doit être activée pour recevoir toutes les données sur le point de terminaison intégré. 

Vous pouvez activer/désactiver la route de secours dans le portail Azure -> Panneau Routage des messages. Vous pouvez également utiliser Azure Resource Manager pour que [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) utilise un point de terminaison personnalisé pour la route de secours.

## <a name="non-telemetry-events"></a>Événements autres que les événements de télémétrie

En plus de la télémétrie des appareils, le routage des messages permet également l’envoi d’événements de modification des jumeaux d’appareil et des événements du cycle de vie des appareils. Par exemple, si une route est créée avec la source de données définie sur **Événements de modification de jumeau d’appareil**, IoT Hub envoie les messages au point de terminaison qui contient la modification du jumeau d’appareil. De même, si une route est créée avec la source de données définie sur **Événements de cycle de vie d’appareil**, IoT Hub envoie un message qui indique si l’appareil a été supprimé ou créé. 

[IoT Hub s’intègre également à Azure Event Grid](iot-hub-event-grid.md) pour publier des événements d’appareils de façon à prendre en charge les intégrations en temps réel et l’automatisation des workflows basés sur ces événements. Pour découvrir ce qui convient le mieux à votre scénario, consultez les [différences principales entre le routage des messages et Event Grid](iot-hub-event-grid-routing-comparison.md).

## <a name="testing-routes"></a>Test des routes

Quand vous créez un nouvelle route ou que modifiez une route existante, vous devez tester la requête de route avec un exemple de message. Vous pouvez tester des routes individuelles ou toutes les routes à la fois. Aucun message n’est routé vers les points de terminaison pendant le test. Vous pouvez utiliser le portail Azure, Azure Resource Manager, Azure PowerShell et Azure CLI pour les tests. Les résultats vous permettent de déterminer si l’exemple de message correspondait ou non à la requête, ou si le test n’a pas pu s’exécuter, l’exemple de message ou la syntaxe de la requête étant incorrects. Pour plus d’informations, consultez [Tester une route](/rest/api/iothub/iothubresource/testroute) et [Tester toutes les routes](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latence

Lorsque vous routez des messages de télémétrie appareil-à-cloud à l’aide de points de terminaison intégrés, une légère augmentation de la latence de bout en bout se produit après la création du premier itinéraire.

Dans la plupart des cas, l’augmentation moyenne de la latence est inférieure à 500 ms. Vous pouvez surveiller la latence avec **Routage : latence des messages de messages/d’événements** ou avec la métrique IoT Hub **d2c.endpoints.latency.builtIn.events**. La création ou la suppression d’un itinéraire après celle du premier itinéraire n’affecte pas la latence de bout en bout.

## <a name="monitoring-and-troubleshooting"></a>Surveillance et dépannage

IoT Hub fournit plusieurs métriques liées aux routages et aux point de terminaison pour vous donner une vue d’ensemble de l’intégrité de votre hub et des messages envoyés. Vous pouvez combiner les informations de plusieurs métriques pour identifier la cause racine des problèmes. Par exemple, utilisez la métrique **Routage : messages de télémétrie annulés** ou **d2c.telemetry.egress.dropped** pour identifier le nombre de messages qui ont été supprimés quand ils ne correspondaient pas aux requêtes sur aucune des routes et que la route de secours était désactivée. [Métriques IoT Hub](iot-hub-metrics.md) liste toutes les métriques activées par défaut pour votre hub IoT.

Grâce aux journaux de diagnostic des **routes** dans les [paramètres de diagnostic](../iot-hub/iot-hub-monitor-resource-health.md) d’Azure Monitor, vous pouvez suivre les erreurs qui se produisent lors de l’évaluation d’une requête de routage et de l’intégrité du point de terminaison telle qu’elle est perçue par IoT Hub, par exemple quand un point de terminaison est inactif. Vous pouvez envoyer ces journaux de diagnostic à Log Analytics, à Event Hubs ou à Stockage Azure pour un traitement personnalisé.

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment créer des routes de messages, consultez [Traiter les messages appareil-à-cloud IoT Hub avec des routes](tutorial-routing.md).

* [Guide pratique pour envoyer des messages appareil-à-cloud](quickstart-send-telemetry-node.md)

* Pour plus d’informations sur les SDK que vous pouvez utiliser pour envoyer des messages appareil-à-cloud, consultez [SDK Azure IoT](iot-hub-devguide-sdks.md).
