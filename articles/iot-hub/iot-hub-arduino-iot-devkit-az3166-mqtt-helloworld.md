---
title: Envoyer des messages à un serveur MQTT avec la bibliothèque de client Azure MQTT
description: Découvrez comment utiliser la bibliothèque de client MQTT pour envoyer des messages à un répartiteur MQTT. Apprenez également à configurer votre appareil IoT DevKit MXChip comme client MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 600e64ef5bc3329f0116359066bdcdaf42c13e2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81733491"
---
# <a name="send-messages-to-an-mqtt-server"></a>Envoyer des messages à un serveur MQTT

Les systèmes Internet des objets (IoT) sont souvent confrontés à des connexions Internet intermittentes, de mauvaise qualité ou lentes. MQTT est un protocole de connectivité machine à machine (M2M) qui a été développé en prenant ces défis en compte. 

La bibliothèque de client MQTT utilisée ici fait partie du projet [Eclipse Paho](https://www.eclipse.org/paho/) qui fournit des API pour l’utilisation de MQTT sur plusieurs moyens de transport.

## <a name="what-you-learn"></a>Contenu

Dans ce projet, vous allez apprendre :
- Comment utiliser la bibliothèque de client MQTT pour envoyer des messages à un répartiteur MQTT.
- Comment configurer votre DevKit IoT MXChip en tant que client MQTT.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Suivez le [Guide de mise en route](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) pour :

* Connecter votre DevKit au Wi-Fi
* Préparer l’environnement de développement

## <a name="open-the-project-folder"></a>Ouvrir le dossier de projet

1. Si le DevKit est déjà connecté à votre ordinateur, déconnectez-le.

2. Démarrez VS Code.

3. Connectez le kit DevKit à votre ordinateur.

## <a name="open-the-mqttclient-sample"></a>Ouvrir l’exemple MQTTClient

Développez la section **EXEMPLES ARDUINO** à gauche, accédez à **Exemples pour MXCHIP AZ3166 > MQTT** et sélectionnez **MQTTClient**. Une nouvelle fenêtre VS Code s’ouvre avec le dossier de projet qu’elle contient.

> [!NOTE]
> Vous pouvez également ouvrir l’exemple à partir de la palette de commandes. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Générer et charger l’ébauche de projet Arduino dans le DevKit

Saisissez `Ctrl+P` (macOS : `Cmd+P`) pour exécuter `task device-upload`. Une fois le chargement terminé, le DevKit et exécute l’ébauche.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Vous pouvez recevoir un message d’erreur « Error: AZ3166: Unknown package » (Erreur : AZ3166 : Package inconnu). Cette erreur se produit lorsque l’index du package de la carte n’est pas actualisé correctement. Pour résoudre cette erreur, consultez la [section de développement du FAQ sur le DevKit IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Tester le projet

Dans VS Code, suivez cette procédure pour ouvrir et configurer Serial Monitor (analyse en série) :

1. Cliquez sur le mot `COM[X]` dans la barre d’état pour définir le port COM approprié avec `STMicroelectronics` : ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Cliquez sur l’icône de branchement « power » dans la barre d’état pour ouvrir l’analyse en série : ![serial-monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Dans la barre d’état, cliquez sur le numéro qui représente la vitesse de transmission et définissez-la sur `115200` : ![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

L’analyse de série affiche tous les messages envoyés par l’exemple d’ébauche. L’ébauche connecte le DevKit en Wi-Fi. Une fois la connexion Wi-Fi établie, l’ébauche envoie un message au répartiteur MQTT. L’exemple envoie ensuite à plusieurs reprises deux messages « iot.eclipse.org » à l’aide de QoS 0 et QoS 1, respectivement.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez le [FAQ sur le DevKit IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou connectez-vous en utilisant les canaux suivants :

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Voir aussi

* [Connecter IoT DevKit AZ3166 à Azure IoT Hub dans le cloud](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake pour un Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer votre DevKit IoT MXChip en tant que client MQTT et à utiliser la bibliothèque de client MQTT pour envoyer des messages à un répartiteur MQTT, voici les étapes suivantes suggérées :

* [Vue d’ensemble des accélérateurs de solution de supervision à distance Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
