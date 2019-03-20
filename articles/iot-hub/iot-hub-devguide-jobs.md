---
title: Présentation des tâches Azure IoT Hub | Microsoft Docs
description: Guide du développeur - Planification des travaux à exécuter sur plusieurs appareils connectés à votre IoT Hub. Les tâches peuvent mettre à jour les balises et les propriétés souhaitées, et appeler des méthodes directes sur plusieurs appareils.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2018
ms.openlocfilehash: c2b05b1854b4f1d7ee4ac65ebe635330ba8c604e
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011362"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Planifier des travaux sur plusieurs appareils

Azure IoT Hub utilise un certain nombre de blocs de construction, tels que les [balises et propriétés de jumeau d’appareil](iot-hub-devguide-device-twins.md) et les [méthodes directes](iot-hub-devguide-direct-methods.md). Généralement, les applications principales permettent aux administrateurs et opérateurs d’appareil de mettre à jour et d’interagir avec les appareils IoT par lots et à une heure planifiée. Les travaux assurent l’exécution des mises à jour des jumeaux d’appareil et des méthodes directes sur un ensemble d’appareils à une heure planifiée. Par exemple, un opérateur peut utiliser une application principale qui lance et suit un travail pour le redémarrage d’un ensemble d’appareils dans le bâtiment 43 à l’étage 3 à une heure qui ne perturbera pas les opérations du bâtiment.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Envisagez d’utiliser les travaux quand vous devez planifier et suivre la progression des activités suivantes sur un ensemble d’appareils :

* Mettre à jour les propriétés souhaitées
* Mettre à jour les balises
* Appeler des méthodes directes

## <a name="job-lifecycle"></a>Cycle de vie de tâche

Les travaux sont lancés par l’application principale de la solution et maintenus par IoT Hub. Vous pouvez lancer un travail via une URI de service (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) et vérifier la progression d’un travail en cours via une URI de service (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Pour actualiser l’état des travaux en cours d’exécution une fois qu’un travail a démarré, exécutez une requête de travail.

> [!NOTE]
> Lorsque vous initiez un travail, les noms et valeurs de propriété peuvent contenir uniquement des caractères alphanumériques US-ASCII imprimables, à l’exception des caractères suivants : `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Tâches pour exécuter des méthodes directes

L’extrait suivant montre les détails de la requête HTTPS 1.1 pour exécuter une [méthode directe](iot-hub-devguide-direct-methods.md) sur un ensemble d’appareils en utilisant un travail :

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleDirectMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

La condition de requête peut également être un ID d’appareil unique ou figurer sur une liste d’ID comme illustré dans les exemples suivants :

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

Le [langage de requête IoT Hub](iot-hub-devguide-query-language.md) couvre le langage de requête IoT Hub de façon plus détaillée.

## <a name="jobs-to-update-device-twin-properties"></a>Travaux pour mettre à jour les propriétés d’un jumeau d’appareil

L’extrait suivant montre les détails de la requête HTTPS 1.1 pour mettre à jour les propriétés d’un jumeau d’appareil à l’aide d’un travail :

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleTwinUpdate",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

## <a name="querying-for-progress-on-jobs"></a>Vérification de la progression des travaux

L’extrait de code suivant montre les détails de la requête HTTPS 1.1 pour interroger des travaux :

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>
```

Le paramètre continuationToken est fourni dans la réponse.

Vous pouvez interroger l’état d’exécution du travail sur chaque appareil à l’aide du [langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Propriétés des travaux

La liste suivante montre les propriétés et les descriptions correspondantes qui peuvent être utilisées lors de la vérification de travaux ou de résultats de travaux.

| Propriété | Description |
| --- | --- |
| **jobId** |L’application a fourni un ID pour le travail. |
| **startTime** |L’application a fourni l’heure de début (ISO-8601) pour le travail. |
| **endTime** |IoT Hub a fourni la date (ISO-8601) de fin du travail. Valide uniquement lorsque la tâche atteint l’état « terminé ». |
| **type** |Types de tâches : |
| | **scheduledUpdateTwin**: Un travail utilisé pour mettre à jour un ensemble de propriétés souhaitées ou tags. |
| | **scheduledDeviceMethod**: Un travail utilisé pour appeler une méthode d’appareil sur un ensemble de représentations d’appareil. |
| **statut** |État actuel du travail. Valeurs possibles pour l'état : |
| | **En attente**: Planifié et en attente d’être récupéré par le service de travail. |
| | **planifiée**: Planifiée pendant une période à l’avenir. |
| | **En cours d’exécution**: Travail est actuellement actif. |
| | **annulé**: Travail a été annulé. |
| | **Échec de**: Échoué de la tâche. |
| | **Terminé**: Tâche terminée. |
| **deviceJobStatistics** |Statistiques relatives à l’exécution du travail. |
| | Propriétés **deviceJobStatistics** : |
| | **deviceJobStatistics.deviceCount**: Nombre d’appareils du travail. |
| | **deviceJobStatistics.failedCount**: Nombre d’appareils sur lesquels le travail a échoué. |
| | **deviceJobStatistics.succeededCount**: Nombre d’appareils sur lesquels le travail a réussi. |
| | **deviceJobStatistics.runningCount**: Nombre d’appareils qui exécutent actuellement le travail. |
| | **deviceJobStatistics.pendingCount**: Nombre d’appareils en attente d’exécution du travail. |

### <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md), qui décrit les différents points de terminaison que chaque hub IoT expose pour les opérations d’exécution et de gestion.

* La rubrique [Quotas et limitation](iot-hub-devguide-quotas-throttling.md) décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre en cas d’utilisation du service.

* La rubrique [SDK des services et appareils Azure IoT](iot-hub-devguide-sdks.md), qui répertorie les SDK en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.

* La rubrique [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages](iot-hub-devguide-query-language.md) décrit le langage de requête IoT Hub. Ce langage vous permet de récupérer à partir d’IoT Hub des informations sur des jumeaux d’appareil et des travaux.

* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub](iot-hub-mqtt-support.md), qui fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes

Pour mettre en pratique certains des concepts décrits dans cet article, consultez le didacticiel IoT Hub suivant :

* [Planifier et diffuser des travaux](iot-hub-node-node-schedule-jobs.md)