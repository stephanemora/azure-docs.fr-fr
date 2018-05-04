---
title: Messages cloud-à-appareil avec Azure IoT Hub (iOS) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour iOS.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 032412c329e79ec671f59a049da7d8ddc0b9dd08
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Envoi de messages cloud à appareil avec IoT Hub (iOS)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. L’article [Send telemetry from a device to an IoT hub (Envoyer des données de télémétrie d’un appareil à un IoT Hub)] explique comment créer un IoT Hub, y approvisionner une identité d’appareil et coder une application d’appareil simulé qui envoie des messages d’appareil-à-cloud.

Cet article vous montre comment procéder.

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.
* Recevez des messages cloud-à-appareil sur un appareil.
* À partir du serveur principal de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages du cloud vers les appareils dans le [Guide du développeur d’IoT Hub][IoT Hub developer guide - C2D].

À la fin de cet article, vous exécutez deux projets iOS Swift :

* **sample-device**, la même application créée dans [Send telemetry from a device to an IoT hub (Envoyer des données de télémétrie d’un appareil à un IoT Hub)], qui se connecte à votre IoT Hub et reçoit des messages cloud-à-appareil.
* **sample-service**, qui envoie un message cloud-à-appareil à l’application d’appareil simulé par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

- Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)
- Un IoT Hub actif dans Azure. 
- L’exemple de code dans les [exemples Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).
- La dernière version de [XCode](https://developer.apple.com/xcode/), exécutant la dernière version du Kit de développement logiciel (SDK) iOS. Ce démarrage rapide a été testé avec XCode 9.3 et iOS 11.3.
- La dernière version de [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).


## <a name="simulate-an-iot-device"></a>Simuler un appareil IoT
Dans cette section, vous simulez un appareil iOS exécutant une application Swift pour recevoir des messages cloud-à-appareil provenant de l’Iot Hub. 

Il s’agit de l’exemple d’appareil que vous créez dans l’article [Send telemetry from a device to an IoT hub (Envoyer des données de télémétrie d’un appareil à un IoT Hub)]. Si vous avez déjà cet appareil, vous pouvez ignorer cette section.

### <a name="install-cocoapods"></a>Installer les CocoaPods

Les CocoaPods gèrent les dépendances des projets iOS qui utilisent des bibliothèques tierces.

Dans une fenêtre de terminal, accédez au dossier Azure-IoT-Samples-iOS que vous avez téléchargé dans les conditions préalables. Ensuite, accédez à l’exemple de projet :

```sh
cd quickstart/sample-device
```

Assurez-vous que XCode est fermé, puis exécutez la commande suivante pour installer les CocoaPods déclarés dans le fichier **podfile** :

```sh
pod install
```

Outre l’installation des pods nécessaires à votre projet, la commande d’installation crée également un fichier d’espace de travail XCode qui est déjà configuré pour utiliser les blocs pour les dépendances. 

### <a name="run-the-sample-device-application"></a>Exécuter l’application d’appareil exemple 

1. Récupérez la chaîne de connexion de votre appareil. Vous pouvez copier cette chaîne à partir du portail Azure dans le panneau des détails de l’appareil ou la récupérer avec la commande CLI suivante : 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Ouvrez l’exemple d’espace de travail dans XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Développez le projet **MQTT Client Sample**, puis le dossier du même nom.  
3. Ouvrez **ViewController.swift** pour le modifier dans XCode. 
4. Recherchez la variable **connectionString** et mettez à jour sa valeur en fonction de la chaîne de connexion d’appareil que vous avez copiée à la première étape.
5. Enregistrez vos modifications. 
6. Exécutez le projet dans l’émulateur d’appareil avec le bouton **Build and run (Générer et exécuter)** ou la combinaison de touches **commande + r**. 

   ![Exécuter le projet](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>Simuler un appareil de service

Dans cette section, vous simulez un deuxième appareil iOS avec une application Swift qui envoie des messages cloud-à-appareil via l’IoT Hub. Cette configuration est utile pour les scénarios d’IoT dans lequel un iPhone ou un iPad fonctionne comme un contrôleur pour les autres appareils iOS connectés à un IoT Hub. 

### <a name="install-cocoapods"></a>Installer les CocoaPods

Les CocoaPods gèrent les dépendances des projets iOS qui utilisent des bibliothèques tierces.

Accédez au dossier Azure IoT iOS Samples que vous avez téléchargé dans les conditions préalables. Ensuite, accédez à l’exemple de projet de service :

```sh
cd quickstart/sample-service
```

Assurez-vous que XCode est fermé, puis exécutez la commande suivante pour installer les CocoaPods déclarés dans le fichier **podfile** :

```sh
pod install
```

Outre l’installation des pods nécessaires à votre projet, la commande d’installation crée également un fichier d’espace de travail XCode qui est déjà configuré pour utiliser les blocs pour les dépendances.

### <a name="run-the-sample-service-application"></a>Exécuter l’exemple d’application de service

1. Récupérez la chaîne de connexion de service de votre IoT Hub. Vous pouvez copier cette chaîne depuis le portail Azure, à partir de la stratégie **iothubowner** dans le panneau **Stratégies d’accès partagé**, ou la récupérer avec la commande CLI suivante :  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Ouvrez l’exemple d’espace de travail dans XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Développez le projet **AzureIoTServiceSample**, puis le dossier du même nom.  
4. Ouvrez **ViewController.swift** pour le modifier dans XCode. 
5. Recherchez la variable **connectionString** et mettez à jour sa valeur en fonction de la chaîne de connexion de service que vous avez copiée précédemment.
6. Enregistrez vos modifications. 
7. Dans Xcode, remplacez les paramètres d’émulateur par un appareil iOS autre que celui utilisé pour exécuter le périphérique IoT. XCode ne peut pas exécuter plusieurs émulateurs du même type. 

   ![Changer l’émulateur d’appareil](media/iot-hub-ios-swift-c2d/change-device.png)

8. Exécutez le projet dans l’émulateur d’appareil avec le bouton **Build and run (Générer et exécuter)** ou la combinaison de touches **commande + r**. 

   ![Exécuter le projet](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil
Vous êtes maintenant prêt à utiliser les deux applications pour envoyer et recevoir des messages cloud-à-appareil.

1. Dans l’application **iOS App Sample** exécutée sur l’appareil IoT simulé, cliquez sur **Démarrer**. L’application commence à envoyer des messages appareil-à-cloud, mais aussi à écouter les messages cloud-à-appareil. 

   ![Afficher l’exemple d’application d’appareil IoT](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Dans l’application **IoTHub Service Client Sample** exécutée sur l’appareil de service simulé, entrez l’ID de l’appareil IoT auquel vous souhaitez envoyer un message. 
3. Écrivez un message en texte brut, puis cliquez sur **Envoyer**. 

Plusieurs actions s’exécutent lorsque vous cliquez sur Envoyer. L’exemple de service envoie le message à votre IoT Hub, auquel l’application a accès, selon la chaîne de connexion de service que vous avez fournie. Votre IoT Hub vérifie l’ID d’appareil, envoie le message à l’appareil de destination et envoie un accusé de réception à l’appareil source. L’application exécutée sur votre appareil IoT simulé recherche les messages provenant de l’IoT Hub et affiche le texte du message le plus récent.

Votre sortie doit ressembler à l’exemple suivant :

   ![Afficher les messages cloud-à-appareil](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil. 

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Azure IoT Suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[Send telemetry from a device to an IoT hub (Envoyer des données de télémétrie d’un appareil à un IoT Hub)]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Guide du développeur IoT Hub]: iot-hub-devguide.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
