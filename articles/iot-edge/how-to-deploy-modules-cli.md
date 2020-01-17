---
title: Déployer des Modules à partir de la ligne de commande Azure CLI - Azure IoT Edge
description: Utilisez Azure CLI avec l’extension Azure IoT pour envoyer un Module IoT Edge de votre IoT Hub à votre appareil IoT Edge, comme configuré par un manifeste de déploiement.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c63180e77a15c6fc7cbee06ad2eb344b50b97ab7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434339"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Déployer des modules Azure IoT Edge avec Azure CLI

Une fois que vous avez créé des modules IoT Edge avec votre logique métier, vous pouvez les déployer sur vos appareils afin qu’ils opèrent à la périphérie. Si plusieurs modules fonctionnent ensemble pour collecter et traiter les données, vous pouvez les déployer tous à la fois et déclarer les règles de routage qui les connectent.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) est un outil en ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure, telles que IoT Edge. Il vous permet de gérer les ressources Azure IoT Hub, les instances de service Device Provisioning et les hubs liés dès l’installation. La nouvelle extension IoT enrichit Azure CLI avec des fonctionnalités telles que la gestion des appareils, et toutes les fonctionnalités IoT Edge.

Cet article explique comment créer un manifeste de déploiement JSON, puis utiliser ce fichier pour étendre le déploiement à un appareil IoT Edge. Pour plus d’informations sur la création d’un déploiement ciblant plusieurs appareils en fonction de leurs balises partagées, consultez [Déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor-cli.md).

## <a name="prerequisites"></a>Conditions préalables requises

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) dans votre abonnement Azure.
* Un [appareil IoT Edge](how-to-register-device.md#register-with-the-azure-cli) avec le runtime IoT Edge installé.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.24 ou ultérieure. Utilisez `az --version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack.
* [Extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Pour plus d’informations sur le fonctionnement et la création des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

Pour déployer des modules avec Azure CLI, enregistrez localement le manifeste de déploiement dans un fichier .json. Vous utiliserez le chemin du fichier dans la section suivante au moment d’exécuter la commande permettant d’appliquer la configuration à votre appareil.

Par exemple, voici un manifeste de déploiement de base comportant un seul module :

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Déployer sur votre appareil

Vous déployez les modules sur votre appareil en appliquant le manifeste de déploiement que vous avez configuré avec les informations des modules.

Remplacez les répertoires par le dossier dans lequel votre manifeste de déploiement est enregistré. Si vous avez utilisé l’un des modèles IoT Edge VS Code, utilisez le fichier `deployment.json` dans le dossier **config** du répertoire de votre solution, et non le fichier `deployment.template.json`.

Pour appliquer la configuration à un appareil IoT Edge, utilisez la commande suivante :

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Le paramètre device-id respecte la casse. Le paramètre content pointe vers le fichier manifeste de déploiement que vous avez enregistré.

   ![az iot edge set-modules output](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Afficher les modules sur votre appareil

Une fois les modules déployés sur votre appareil, vous pouvez les voir tous à l’aide de la commande suivante :

Affichez les modules sur votre appareil IoT Edge :

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Le paramètre device-id respecte la casse.

   ![az iot hub module-identity list output](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
