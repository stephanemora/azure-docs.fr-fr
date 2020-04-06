---
title: Méthodes directes edgeAgent intégrées - Azure IoT Edge
description: Surveiller et gérer un déploiement IoT Edge à l’aide de méthodes directes intégrées dans le module de runtime de l’agent IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240344"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Communiquer avec edgeAgent à l’aide de méthodes directes intégrées

Surveillez et gérez les déploiements d’IoT Edge à l’aide des méthodes directes incluses dans le module de l’agent IoT Edge. Les méthodes directes sont implémentées sur l’appareil, puis peuvent être appelées à partir du cloud. L’agent IoT Edge comprend des méthodes directes qui vous aident à surveiller et gérer vos appareils IoT Edge à distance.

Pour plus d’informations sur les méthodes directes, leur utilisation et la façon de les implémenter dans vos propres modules, consultez [Comprendre et appeler des méthodes directes à partir d’IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Les noms de ces méthodes directes sont gérés sans tenir compte de la casse.

## <a name="ping"></a>Ping

La méthode **ping** est utile pour vérifier si IoT Edge s’exécute sur un appareil, ou si l’appareil dispose d’une connexion ouverte à IoT Hub. Utilisez cette méthode directe pour envoyer une requête ping à l'agent IoT Edge et obtenir son état. Une commande ping réussie retourne une charge utile vide et **"status": 200**.

Par exemple :

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Dans le portail Azure, appelez la méthode avec le nom de la méthode `ping` et une charge utile JSON vide `{}`.

![Appeler la méthode directe « ping » dans le portail Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Redémarrer le module

La méthode **RestartModule** permet la gestion à distance des modules qui s’exécutent sur un appareil IoT Edge. Si un module signale un état d’échec ou un autre comportement anormal, vous pouvez déclencher l’agent IoT Edge pour le redémarrer. Une commande de redémarrage réussie renvoie une charge utile vide et **"status": 200**.

La méthode RestartModule est disponible dans IoT Edge version 1.0.9 et versions ultérieures. 

Vous pouvez utiliser la méthode directe RestartModule sur n’importe quel module s’exécutant sur un appareil IoT Edge, y compris le module edgeAgent lui-même. Toutefois, si vous utilisez cette méthode directe pour arrêter edgeAgent, vous ne recevrez pas de résultat de réussite, car la connexion est interrompue lors du redémarrage du module.

Par exemple :

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Dans le portail Azure, appelez la méthode avec le nom de la méthode `RestartModule` et la charge utile JSON suivante :

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Appeler la méthode directe ’RestartModule’ dans le portail Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Méthodes expérimentales

De nouvelles options de méthode directe sont disponibles en tant que fonctionnalités expérimentales à tester, notamment :

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md) : Récupérez les journaux de module et chargez-les dans le Stockage Blob Azure.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus) : Vérifiez l’état d’une demande de chargement des journaux.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs) : Récupérez les journaux de module en les intégrant dans la réponse de la méthode directe.

## <a name="next-steps"></a>Étapes suivantes

[Propriétés des jumeaux de module de l’agent IoT Edge et du hub IoT Edge](module-edgeagent-edgehub.md)
