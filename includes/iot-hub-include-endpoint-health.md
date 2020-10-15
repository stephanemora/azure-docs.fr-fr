---
title: Fichier include
description: Fichier include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792085"
---
Vous pouvez utiliser l’API REST [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) pour obtenir l’état d’intégrité des points de terminaison. Nous vous recommandons d’utiliser les [métriques IoT Hub](../articles/iot-hub/iot-hub-metrics.md) associées à la latence de routage des messages pour identifier et déboguer des erreurs lorsque le point de terminaison est inactif ou n’est pas sain, car nous nous attendons à ce que la latence soit plus élevée lorsque le point de terminaison se trouve dans l’un des états indiqués ci-dessous.


|État d’intégrité|Description|
|---|---|
|healthy|Le point de terminaison accepte des messages comme prévu.|
|unhealthy|Le point de terminaison n’accepte pas les messages et IoT Hub effectue une nouvelle tentative d’envoyer des messages à ce point de terminaison.|
|unknown|IoT Hub n’a pas tenté de remettre des messages à ce point de terminaison.|
|degraded|Le point de terminaison accepte les messages plus lentement que prévu ou récupère d’un état non sain.|
|dead|IoT Hub ne remet plus de messages à ce point de terminaison. Les nouvelles tentatives d’envoi de messages à ce point de terminaison ont échoué.|
