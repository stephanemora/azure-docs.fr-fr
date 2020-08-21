---
title: Intégration à d’autres services
titleSuffix: Azure Digital Twins
description: Comprendre les exigences d’entrée et de sortie lors du déploiement d’Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ca500401a6bff8a00dd9c51eecb29aa93fdbc82b
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042647"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Intégrer Azure Digital Twins avec d’autres services

Azure Digital Twins est généralement utilisé avec d’autres services. À l’aide d’[**itinéraires d’événements**](concepts-route-events.md), Azure Digital Twins reçoit des données de services en amont, tel [IOT Hub](../iot-hub/about-iot-hub.md), qui sont utilisés pour fournir de la télémétrie et des notifications. 

Azure Digital Twins peut également acheminer des données vers des services en aval, tels qu’Azure Maps ([*Guide pratique : Utiliser Azure Digital Twins pour mettre à jour une carte d’intérieur Azure Maps*](how-to-integrate-maps.md)) et Time Series Insights ([*Guide pratique : Intégration à Time Series Insights*](how-to-integrate-time-series-insights.md)), à des fins de stockage, d’intégration de workflow, d’analytique et bien plus encore. 

## <a name="data-ingress"></a>Entrée de données

Azure Digital Twins peut être piloté par des données et des événements issus d’un service quel qu’il soit – IoT Hub, Logic Apps, votre propre service personnalisé, etc. Cela vous permet de collecter des données de télémétrie à partir d’appareils physiques dans votre environnement, puis de les traiter à l’aide du graphique Azure Digital Twins dans le cloud.

Azure Digital Twins n’intègre pas de IoT Hub. Vous pouvez utiliser un IoT Hub existant actuellement en production ou en déployer un nouveau. Vous bénéficiez ainsi d’un accès complet à toutes les fonctionnalités de gestion d’appareils d’IoT Hub.

Pour ingérer des données issues d’une source dans Azure Digital Twins, utilisez une [fonction Azure](../azure-functions/functions-overview.md). Pour plus d’informations sur ce modèle, consultez [*Guide pratique : Ingérer la télémétrie depuis IoT Hub*](how-to-ingest-iot-hub-data.md) ou essayez-le dans le document Azure Digital Twins [*Tutoriel : Connecter une solution de bout en bout*](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Services de sortie des données

Azure Digital Twins peut envoyer des données à des **points de terminaison** connectés. Les points de terminaison pris en charge peuvent être :
* [Concentrateur d’événements](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Les points de terminaison sont attachés au service Azure Digital Twins à l’aide d’API de gestion ou du portail Azure. Apprenez-en davantage sur la façon d’attacher un point de terminaison à Azure Digital Twins dans [*Guide pratique : Gérer les points de terminaison et les routes*](how-to-manage-routes-apis-cli.md).

Il existe de nombreux autres services vers lesquels vous pouvez finalement diriger vos données, tels que [Stockage Azure](../storage/common/storage-introduction.md) ou [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Pour envoyer vos données à de tels services, attachez le service de destination à un point de terminaison.

Par exemple, si vous utilisez également [Azure Maps](../azure-maps/about-azure-maps.md) et souhaitez mettre en corrélation l’emplacement avec votre [graphique de jumeau](concepts-twins-graph.md) Azure Digital Twins, vous pouvez utiliser Azure Functions avec Event Grid pour établir la communication entre tous les services au sein de votre déploiement.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les points de terminaison et le routage des événements vers des services externes :
* [*Concepts : routage des événements Azure Digital Twins*](concepts-route-events.md)

Découvrez comment configurer Azure Digital Twins pour ingérer des données à partir d’IoT Hub :
* [*Guide pratique : Ingérer la télémétrie depuis IoT Hub*](how-to-ingest-iot-hub-data.md)
