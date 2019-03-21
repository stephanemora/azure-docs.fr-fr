---
title: Démarrage rapide pour envoyer des données de télémétrie à Azure IoT Hub | Microsoft Docs
description: Dans ce démarrage rapide, vous exécutez un exemple d’application iOS pour envoyer des données de télémétrie simulées à un hub IoT et lire les données de télémétrie provenant du hub IoT à traiter dans le cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/20/2019
ms.openlocfilehash: 5def12acf3aa87c06ea7cd2f8a1102018750d36d
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009492"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Démarrage rapide : Envoyer des données de télémétrie à partir d’un appareil vers un hub IoT (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub est un service Azure qui vous permet de traiter de gros volumes de données de télémétrie envoyées par vos appareils IoT dans le cloud à des fins de stockage ou de traitement. Dans cet article, vous envoyez des données de télémétrie depuis une application d’appareil simulé à IoT Hub. Vous pouvez afficher les données à partir d’une application principale. 

Cet article utilise une application Swift prédéfinie pour envoyer des données de télémétrie, et un utilitaire CLI pour lire ces données de télémétrie provenant d’IoT Hub. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Télécharger l’exemple de code depuis [Exemples Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) 
- La dernière version de [XCode](https://developer.apple.com/xcode/), exécutant la dernière version du Kit de développement logiciel (SDK) iOS. Ce démarrage rapide a été testé avec XCode 9.3 et iOS 11.3.
- La dernière version de [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour ajouter l’extension CLI IoT Hub et créer l’identité d’appareil. 

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **myiOSdevice** : il s’agit du nom donné à l’appareil inscrit. Utilisez MyiOSdevice comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez également utiliser ce nom pour l’ensemble de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   az iot hub device-identity create --hub-name YourIoTHubName --device-id myiOSdevice
   ```

1. Exécutez la commande suivante pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id myiOSdevice --output table
   ```

   Notez la chaîne de connexion à l’appareil, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide.

## <a name="send-simulated-telemetry"></a>Envoyer des données de télémétrie simulée

L’exemple d’application s’exécute sur un appareil iOS, qui se connecte à un point de terminaison spécifique de l’appareil sur votre hub IoT et envoie les données de télémétrie simulée (température et humidité). 

### <a name="install-cocoapods"></a>Installer les CocoaPods

Les CocoaPods gèrent les dépendances des projets iOS qui utilisent des bibliothèques tierces.

Dans une fenêtre de terminal local, accédez au dossier Azure-IoT-Samples-iOS que vous avez téléchargé dans les conditions préalables. Ensuite, accédez à l’exemple de projet :

```sh
cd quickstart/sample-device
```

Assurez-vous que XCode est fermé, puis exécutez la commande suivante pour installer les CocoaPods déclarés dans le fichier **podfile** :

```sh
pod install
```

Outre l’installation des pods nécessaires à votre projet, la commande d’installation crée également un fichier d’espace de travail XCode qui est déjà configuré pour utiliser les blocs pour les dépendances. 

### <a name="run-the-sample-application"></a>Exécuter l’exemple d’application 

1. Ouvrez l’exemple d’espace de travail dans XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Développez le projet **MQTT Client Sample**, puis le dossier du même nom.  
3. Ouvrez **ViewController.swift** pour le modifier dans XCode. 
4. Recherchez la variable **connectionString** et mettez à jour sa valeur en fonction de la chaîne de connexion d’appareil que vous avez notée précédemment.
5. Enregistrez vos modifications. 
6. Exécutez le projet dans l’émulateur d’appareil avec le bouton **Build and run (Générer et exécuter)** ou la combinaison de touches **commande + r**. 

   ![Exécuter le projet](media/quickstart-send-telemetry-ios/run-sample.png)

7. Lorsque l’émulateur s’ouvre, sélectionnez **Démarrer** dans l’exemple d’application.

La capture d’écran suivante présente quelques exemples de sortie lorsque l’application envoie des données de télémétrie simulée à votre hub IoT :

   ![Exécuter l’appareil simulé](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Lire les données de télémétrie envoyées par votre hub

L’exemple d’application que vous avez exécutée sur l’émulateur XCode affiche les données concernant les messages envoyés par l’appareil. Vous pouvez également voir les données à votre hub IoT, au fur et à mesure de leur réception. L’extension CLI IoT Hub peut se connecter au point de terminaison **Événements** côté service sur votre IoT Hub. L’extension reçoit les messages appareil-à-cloud envoyés à partir de votre appareil simulé. Une application back-end IoT Hub s’exécute généralement dans le cloud pour recevoir et traiter les messages appareil-à-cloud.

Exécutez les commandes suivantes dans Azure Cloud Shell, en remplaçant `YourIoTHubName` par le nom de votre hub IoT :

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name YourIoTHubName
```

La capture d’écran suivante présente la sortie lorsque l’extension reçoit les données de télémétrie envoyées par l’appareil simulé au hub :

La capture d’écran suivante affiche le type de données de télémétrie que vous voyez dans votre fenêtre de terminal local :

![Afficher les données de télémétrie](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez configuré un hub IoT, inscrit un appareil, envoyé des données de télémétrie simulée au hub à partir d’un appareil iOS et lu ces données de télémétrie provenant du hub. 

Pour savoir comment contrôler votre appareil simulé à partir d’une application back-end, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Démarrage rapide : Contrôler un appareil connecté à un hub IoT](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
