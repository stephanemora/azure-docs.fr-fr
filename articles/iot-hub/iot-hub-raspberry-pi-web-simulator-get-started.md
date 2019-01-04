---
title: Raspberry Pi vers cloud (Node.js) simulé - Connecter le simulateur web Raspberry Pi à Azure IoT Hub | Microsoft Docs
description: Connectez le simulateur web Raspberry Pi à Azure IoT Hub pour que Raspberry Pi puisse envoyer des données vers le cloud Azure.
author: rangv
manager: ''
keywords: simulateur raspberry, azure iot raspberry pi, raspberry pi iot hub, raspberry pi envoie des données vers le cloud, raspberry pi vers cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 6844fb9b6e85e77ad9c7a869a989214fffd05ece
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438742"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Connecter le simulateur en ligne Raspberry Pi à Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation du simulateur en ligne Raspberry Pi. Vous apprenez ensuite à connecter en toute transparence le simulateur Pi au cloud à l’aide d’[Azure IoT Hub](about-iot-hub.md). 

Si vous avez des appareils physiques, voir [Connecter Raspberry Pi à Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) pour commencer. 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>Procédure

* Découvrez les principes fondamentaux du simulateur en ligne Raspberry Pi.
* Créez un hub IoT.
* Inscrivez un appareil pour Pi dans votre IoT Hub.
* Exécutez un exemple d’application sur Pi pour envoyer des données de capteur à votre IoT Hub.

Connectez le Raspberry Pi simulé à un IoT Hub que vous créez. Exécutez ensuite un exemple d’application avec le simulateur pour générer des données de capteur. Enfin, envoyez les données du capteur à votre IoT Hub.

## <a name="what-you-learn"></a>Contenu

* Création d’un Azure IoT Hub et obtention de la chaîne de connexion de votre nouvel appareil. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation Azure gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* Utilisation du simulateur en ligne Raspberry Pi.
* Envoi des données du capteur à votre IoT Hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Vue d’ensemble du simulateur web Raspberry Pi

Cliquez sur le bouton pour lancer le simulateur en ligne Raspberry Pi.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Démarrer le simulateur Raspberry Pi</a>

Le simulateur web comprend trois zones.

1. Zone d’assemblage : le circuit par défaut est celui qu’un Pi connecte à un capteur BME280 et à une DEL. La zone étant verrouillée dans la préversion, vous ne pouvez pas apporter de personnalisation.

2. Zone de codage : éditeur de code en ligne permettant de coder avec Raspberry Pi. L’exemple d’application par défaut aide à collecter les données du capteur BME280 et à les envoyer à votre Azure IoT Hub. L’application est entièrement compatible avec les appareils Pi réels. 

3. Fenêtre de console intégrée : montre la sortie de votre code. En haut de cette fenêtre figurent trois boutons.

   * **Exécuter** : exécuter l’application dans la zone de codage.
   * **Réinitialiser**: réinitialiser la zone de codage pour l’exemple d’application par défaut.
   * **Plier/Développer** : situé du côté droit, ce bouton permet de plier/développer la fenêtre de console.

> [!NOTE] 
Le simulateur web Raspberry Pi est désormais disponible en préversion. Nous aimerions entendre votre voix dans le [salon de conversation Gitter](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Le code source est public et disponible sur [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Vue d’ensemble du simulateur en ligne Pi](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Récupérer la chaîne de connexion pour le hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Exécuter un exemple d’application sur le simulateur web Pi

1. Dans la zone de codage, assurez-vous que vous travaillez dans l’exemple d’application par défaut. Remplacez l’espace réservé à la ligne 15 par la chaîne de connexion d’appareil Azure IoT Hub.
   ![Remplacer la chaîne de connexion d’appareil](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Cliquez sur **Exécuter** ou tapez `npm start` pour exécuter l’application.

Vous devriez voir le résultat suivant, qui affiche les données de capteur et les messages envoyés à votre IoT Hub ![Sortie - données de capteur envoyées du Raspberry Pi à votre IoT hub](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Étapes suivantes

Vous avez exécuté un exemple d’application pour collecter des données de capteur et les envoyer à votre IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
