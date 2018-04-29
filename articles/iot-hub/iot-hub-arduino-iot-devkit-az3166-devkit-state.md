---
title: Utiliser des jumeaux d’appareils Azure pour contrôler le voyant utilisateur de l’appareil DevKit IoT MXChip | Microsoft Docs
description: Dans ce tutoriel, découvrez comment surveiller les états du Kit de développement et contrôler le voyant utilisateur avec les jumeaux d’appareils Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 33d8a36cc88bd1c263f2c4a38a59e04e1253357c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="mxchip-iot-devkit"></a>DevKit IoT MXChip

Vous pouvez utiliser cet exemple pour surveiller les informations relatives au Wi-Fi et les états du capteur de l’appareil DevKit IoT MXChip, ainsi que pour contrôler la couleur du voyant utilisateur avec les jumeaux d’appareil Azure IoT Hub.

## <a name="what-you-learn"></a>Contenu

- Comment surveiller les états du capteur de l’appareil DevKit IoT MXChip.
- Comment utiliser les jumeaux d’appareils Azure pour contrôler la couleur du voyant utilisateur RVB du Kit de développement.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Configurez votre environnement de développement en suivant le [Guide de prise en main](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
- À partir de votre fenêtre de terminal GitBash (ou d’une autre interface de ligne de commande Git), tapez les commandes suivantes :
    - `git clone https://github.com/DevKitExamples/DevKitState.git`
    - `cd DevKitState`
    - `code .`

## <a name="provision-azure-services"></a>Approvisionner les services Azure

1. Cliquez sur le menu déroulant **Tâches** dans Visual Studio Code, puis sélectionnez **Exécuter la tâche...**  - **cloud-provision**.
2. Votre progression s’affiche sous l’onglet **TERMINAL** du panneau **Bienvenue**.
3. Lorsque vous y êtes invité par le message vous demandant*quel abonnement choisir*, sélectionnez un abonnement.
4. Sélectionnez ou choisissez un groupe de ressources. 
 
    > [!NOTE]
    > Si vous disposez déjà d’un IoT Hub gratuit, cette étape sera ignorée.

5. Lorsque vous y êtes invité par le message vous demandant*quel IoT Hub choisir*, sélectionnez ou créez un IoT Hub.
6. Un texte ressemblant à ce qui suit : *application de fonction : nom de l’application de fonction : xxx*, s’affiche. Notez le nom de l’application de fonction, qui sera utilisée ultérieurement.
7. Attendez la fin du déploiement du modèle Azure Resource Manager indiquée lorsque le message *Resource Manager template deployment: Done* (Déploiement du modèle Resource Manager : terminé) s’affiche.

## <a name="deploy-function-app"></a>Déployer l’application de fonction

1. Cliquez sur le menu déroulant **Tâches** dans Visual Studio Code, puis sélectionnez **Exécuter la tâche...**  - **cloud-deploy**.
2. Attendez la fin du processus de téléchargement du code de l’application de fonction. Le message *function app deploys: Done* (déploiement de l’application de fonction : terminé) s’affiche.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Configurer la chaîne de connexion d’appareil Azure IoT Hub dans le Kit de développement

1. Connectez votre appareil DevKit IoT MXChip à votre ordinateur.
2. Cliquez sur le menu déroulant **Tâches** dans Visual Studio Code, puis sélectionnez **Exécuter la tâche...**  - **cloud-connection**.
3. Sur l’appareil DevKit IoT MXChip, appuyez sur le bouton **A** tout en le maintenant enfoncé, appuyez sur le bouton **Réinitialiser**, puis relâchez le bouton **A** de sorte que le Kit de développement passe en mode de configuration.
4. Attendez la fin du processus de configuration de la chaîne de connexion.

## <a name="upload-arduino-code-to-devkit"></a>Chargez le code Arduino dans le Kit de développement

Avec l’appareil DevKit IoT MXChip connecté à votre ordinateur :
1. Cliquez sur le menu déroulant **Tâches** dans Visual Studio Code, puis sélectionnez **Exécuter la tâche...**  L’ébauche de projet Arduino est compilée et chargée sur le Kit de développement.
2. Une fois l’esquisse chargée, le message *Build & Upload Sketch: success* (création et chargement de l’ébauche de projet : terminé) s’affiche.

## <a name="monitor-devkit-state-in-browser"></a>Surveiller l’état du Kit de développement dans le navigateur

1. Dans un navigateur Web, ouvrez le fichier `DevKitState\web\index.html`, créé au cours de l’étape [Ce dont vous avez besoin](#whatyouneed).
2. La page web suivante s’ouvre :![Indiquez le nom de l’application de fonction.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)
1. Entrez le nom de l’application de fonction notée précédemment.
2. Cliquez sur le bouton **Connecter**.
3. Après quelques secondes, la page s’actualise et affiche l’état de la connexion Wi-Fi du Kit de développement et l’état de chacun des capteurs intégrés.

## <a name="control-the-devkits-user-led"></a>Contrôler le voyant utilisateur du Kit de développement

1. Cliquez sur le graphique du voyant utilisateur sur l’illustration de la page Web.
2. Après quelques secondes, l’écran s’actualise et affiche l’état actuel de la couleur du voyant utilisateur.
3. Essayez de changer la valeur de la couleur du voyant RVB en cliquant à différents endroits des commandes RVB à curseur.

## <a name="example-operation"></a>Exemple d’opération

![Exemple de procédure de test](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les actions suivantes :
- Connecter un appareil DevKit IoT MXChip à votre Azure IoT Suite.
- Utilisation la fonction de jumeaux d’appareil Azure IoT pour détecter et contrôler la couleur du voyant RVB du Kit de développement.

Voici les prochaines étapes suggérées :

* [Vue d’ensemble d’Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Connecter un appareil DevKit IoT MXChip à votre application Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
