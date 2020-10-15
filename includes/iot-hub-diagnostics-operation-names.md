---
title: Fichier include
description: Fichier include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 362c13771e7382ead1ba5aebd99a69fd86cd718c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793270"
---
<!-- operation names for the diag logs for IoT Hub -->

|Nom d’opération|Level|Description|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Information|Le message ne peut pas être évalué avec une condition d’attribution. Par exemple, si une propriété de la condition de requête d’itinéraire est absente du message. En savoir plus sur la [syntaxe de requête de routage](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).|
|RouteEvaluationError|Error|Une erreur s’est produite lors de l’évaluation du message en raison d’un problème avec le format du message. Par exemple, cette erreur est journalisée si l’encodage du contenu n’est pas spécifié ou si le type de contenu n’est pas valide dans le message. Cela doit être défini dans les [propriétés du système](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties).|
|DroppedMessage|Error|Le message a été supprimé et n’a pas été routé. Cela peut être dû à des raisons telles que le message ne correspond à aucune requête de routage ou que le point de terminaison était mort et que le message n’a pas pu être remis après plusieurs tentatives. Nous vous recommandons d’obtenir plus de détails sur le point de terminaison à l’aide de l’API REST [Obtenir l’intégrité du point de terminaison](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointUnhealthy|Error|Le point de terminaison n’a pas accepté les messages du IoT Hub et le IoT Hub tente de renvoyer les messages. Nous vous recommandons d’observer la dernière erreur connue via l’API REST [Obtenir l’intégrité du point de terminaison](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointDead|Error|Le point de terminaison n’a pas accepté les messages du IoT Hub pendant plus d’une heure. Nous vous recommandons d’observer la dernière erreur connue via l’API REST [Obtenir l’intégrité du point de terminaison](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointHealthy|Information|Le point de terminaison est sain et reçoit des messages du IoT Hub. Ce message n’est pas journalisé en continu, mais enregistré uniquement lorsque le point de terminaison redevient sain. Ce message signifie que le IoT Hub n’a pas pu envoyer des messages au point de terminaison, mais que le point de terminaison est maintenant sain.|
|OrphanedMessage|Information|Le message ne correspond à aucun itinéraire.|
|InvalidMessage|Error|Le message n’est pas valide en raison d’une incompatibilité avec le point de terminaison. Nous vous recommandons de vérifier les configurations du point de terminaison.|


Les opérations *UndefinedRouteEvaluation*, *RouteEvaluationError* et *OrphanedMessage* sont limitées et enregistrées au plus une fois par minute par IoT Hub.