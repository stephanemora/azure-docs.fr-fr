---
title: Déployer des modules à partir de Visual Studio Code - Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour déployer des modules sur un appareil IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126407"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Déployer des modules Azure IoT Edge à partir de Visual Studio Code

Une fois que vous avez créé des modules IoT Edge avec votre logique métier, vous pouvez les déployer sur vos appareils afin qu’ils opèrent à la périphérie. Si plusieurs modules fonctionnent ensemble pour collecter et traiter les données, vous pouvez les déployer tous à la fois et déclarer les règles de routage qui les connectent. 

Cet article explique comment créer un manifeste de déploiement JSON, puis utiliser ce fichier pour étendre le déploiement à un appareil IoT Edge. Pour plus d’informations sur la création d’un déploiement ciblant plusieurs appareils en fonction de leurs balises partagées, consultez [Déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Conditions préalables

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure. 
* Un [appareil IoT Edge](how-to-register-device-portal.md) avec le runtime IoT Edge installé. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) pour Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Pour plus d’informations sur le fonctionnement et la création des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

Pour déployer des modules à l’aide de Visual Studio Code, enregistrez localement le manifeste de déploiement dans un fichier .JSON. Vous utiliserez le chemin du fichier dans la section suivante au moment d’exécuter la commande permettant d’appliquer la configuration à votre appareil.

Par exemple, voici un manifeste de déploiement de base comportant un seul module :

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
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
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
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```
   
## <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

Vous pouvez utiliser les extensions Azure IoT pour Visual Studio Code afin d’effectuer des opérations avec votre hub IoT. Pour que ces opérations fonctionnent, vous devez vous connecter à votre compte Azure, puis sélectionner le hub IoT sur lequel vous travaillez.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.

2. En bas de l’Explorateur, développez la section **Appareils Azure IoT Hub**. 

   ![Développer la section Appareils Azure IoT Hub](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Cliquez sur **...** dans l’en-tête de section **Appareils Azure IoT Hub**. Si vous ne voyez pas les points de suspension, pointez sur l’en-tête. 

4. Choisissez **Sélectionner un hub IoT**.

5. Si vous n’êtes pas connecté à votre compte Azure, suivez les invites à cette fin. 

6. Sélectionnez votre abonnement Azure. 

7. Sélectionnez votre hub IoT. 


## <a name="deploy-to-your-device"></a>Déployer sur votre appareil

Vous déployez les modules sur votre appareil en appliquant le manifeste de déploiement que vous avez configuré avec les informations des modules. 

1. Dans la vue de l’Explorateur Visual Studio Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

1. Cliquez avec le bouton droit sur l’appareil IoT Edge que vous souhaitez configurer avec le manifeste de déploiement.

    > [!TIP]
    > Pour vous assurer que l'appareil choisi est un appareil IoT Edge, sélectionnez-le afin de développer la liste des modules et vérifiez la présence de **$edgeHub** et **$edgeAgent**. Chaque appareil IoT Edge inclut ces deux modules.

1. Sélectionnez **Créer un déploiement pour un seul appareil**.

4. Recherchez le fichier JSON de manifeste de déploiement à utiliser, puis cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge). 

   ![Sélectionner un manifeste de déploiement Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Les résultats de votre déploiement apparaissent dans la sortie de VS Code. Les déploiements réussis sont appliqués après quelques minutes si l’appareil cible est en cours d’exécution et est connecté à internet. 

## <a name="view-modules-on-your-device"></a>Afficher les modules sur votre appareil

Une fois les modules déployés sur votre appareil, vous pouvez les voir tous dans la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). Sélectionnez la flèche en regard de votre appareil IoT Edge pour le développer. Tous les modules en cours d’exécution sont affichés. 

Si vous avez récemment déployé de nouveaux modules sur un appareil, placez le curseur sur l’en-tête de section **Azure IoT Hub Devices** (Appareils Azure IoT Hub), puis sélectionnez l’icône d’actualisation pour mettre à jour la vue. 

Cliquez avec le bouton droit sur le nom d’un module pour afficher et modifier le jumeau de module. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).