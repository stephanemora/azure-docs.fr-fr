---
title: Comment déployer le module de représentations de OPC pour Azure à partir de zéro | Microsoft Docs
description: Comment déployer des représentations d’OPC à partir de zéro.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451070"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Déployer le module de représentations de OPC et les dépendances à partir de zéro

Le module jumeau de OPC s’exécute sur IoT Edge et fournit plusieurs services edge au jumeau d’appareil OPC et les services de Registre. 

Il existe plusieurs options pour déployer des modules à votre [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) passerelle, entre eux

- [Déploiement à partir du Panneau de IoT Edge du portail Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Déploiement à l’aide de AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Pour plus d’informations sur les détails de déploiement et des instructions, consultez le GitHub [référentiel](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Manifeste de déploiement

Tous les modules sont déployés à l’aide d’un manifeste de déploiement.  Un exemple de manifeste pour déployer les deux [Éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher) et [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) est indiqué ci-dessous.

```json
{
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
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
            }
          }
        },
        "modules": {
          "opctwin": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Déploiement à partir du portail Azure

Le moyen le plus simple pour déployer les modules sur un appareil de passerelle Azure IoT Edge est via le portail Azure.  

### <a name="prerequisites"></a>Conditions préalables

1. Déployer la représentation d’OPC [dépendances](howto-opc-twin-deploy-dependencies.md) et obtenu résultant `.env` fichier. Notez déployé `hub name` de la `PCS_IOTHUBREACT_HUB_NAME` variable résultant `.env` fichier.

2. Inscrire et démarrer un [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) ou [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) passerelle IoT Edge et notez son `device id`.

### <a name="deploy-to-an-edge-device"></a>Déployer sur un appareil edge

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre IoT Hub.

2. Sélectionnez **IoT Edge** dans le menu de gauche.

3. Cliquez sur l’ID de l’appareil cible dans la liste des appareils.

4. Sélectionnez **Définir modules**.

5. Dans le **modules de déploiement** section de la page, sélectionnez **ajouter** et **Module IoT Edge.**

6. Dans le **Module personnalisé IoT Edge** utilisation de la boîte de dialogue `opctwin` comme nom pour le module, puis spécifiez le conteneur *URI d’image* en tant que

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   En tant que *créer options* utilisez le JSON suivant :

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   Renseignez les champs facultatifs si nécessaire. Pour plus d’informations sur les options de création de conteneur, la stratégie de redémarrage et l’état souhaité, consultez [Propriétés souhaitées pour EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Pour plus d’informations sur le jumeau de module, consultez [Définir ou mettre à jour les propriétés souhaitées](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Sélectionnez **enregistrer** et répétez l’étape **5**.  

8. Dans la boîte de dialogue du Module personnalisé IoT Edge, utilisez `opcpublisher` comme nom pour le module et le conteneur *URI d’image* en tant que 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   En tant que *créer options* utilisez le JSON suivant :

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Sélectionnez **enregistrer** , puis **suivant** à passer à la section d’itinéraires.

10. Dans l’onglet d’itinéraires, collez le code suivant 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    Sélectionnez **suivant**

11. Passez en revue les informations de votre déploiement et le manifeste.  Il doit ressembler le manifeste de déploiement ci-dessus.  Sélectionnez **Envoyer**.

12. Une fois les modules déployés sur votre appareil, vous pouvez les voir tous dans la page **Détails de l’appareil** du portail. Cette page affiche le nom de chaque module déployé, ainsi que des informations utiles telles que le code de sortie et l’état du déploiement.

## <a name="deploying-using-azure-cli"></a>Déploiement à l’aide d’Azure CLI

### <a name="prerequisites"></a>Conditions préalables

1. Installez la dernière version de la [interface de ligne de commande Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) de [ici](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Démarrage rapide

1. Enregistrez le manifeste de déploiement ci-dessus dans un `deployment.json` fichier.  

2. Pour appliquer la configuration à un appareil IoT Edge, utilisez la commande suivante :

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Le `device id` paramètre respecte la casse. Le paramètre content pointe vers le fichier manifeste de déploiement que vous avez enregistré. 
    ![sortie de set-modules IoT Edge AZ](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Une fois les modules déployés sur votre appareil, vous pouvez les voir tous à l’aide de la commande suivante :

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Le paramètre d’ID de périphérique respecte la casse. ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>Exécuter et déboguer localement

Pour des problèmes dépannage et débogage il sont utile d’exécuter les modules Edge localement à l’aide de la [simulateur de développement IoT Edge](https://github.com/Azure/iotedgehubdev).  Il fournit une expérience de développement local avec un simulateur pour la création, le développement, test, en cours d’exécution et le débogage de modules Azure IoT Edge et les solutions à l’aide de la même bits/code et qui sont utilisées en production.

### <a name="prerequisites"></a>Conditions préalables

1. Déployer la représentation d’OPC [dépendances](howto-opc-twin-deploy-dependencies.md).

2. Installer [Docker CE (18.02.0+)](https://www.docker.com/community-edition) sur [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) ou [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Installer [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (nécessaire uniquement pour les **Linux**. COMPOSE a déjà été inclus dans l’installation de Windows/Mac OS Docker CE)

4. Installer [Python (2.7 / 3.5+) et Pip](https://www.python.org/)

5. Installer iotedgehubdev en exécutant la commande dans votre terminal ci-dessous

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Installer `iotedgehubdev` à **racine** sur Linux/Mac OS (*n’utilisez pas '--utilisateur ' option dans la commande « installer pip »*).
> Assurez-vous qu’il n’existe aucun runtime Azure IoT Edge en cours d’exécution sur le même ordinateur avec iotedgehubdev puisqu’elles nécessitent les mêmes ports.

### <a name="quickstart"></a>Démarrage rapide

1. Suivez les instructions pour [créer un appareil Edge dans le portail Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Copiez la chaîne de connexion de périphérique de périmètre.

2. Configurer le simulateur à l’aide de la chaîne de connexion de bord.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Copie au-dessus de manifeste dans un `deployment.json` fichier dans le même dossier.  Démarrer le déploiement dans le simulateur à l’aide

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Arrêter le simulateur à l’aide

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à déployer des représentations d’OPC à partir de zéro, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Déployer la représentation d’OPC à un projet existant](howto-opc-twin-deploy-existing.md)