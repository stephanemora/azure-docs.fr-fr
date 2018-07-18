---
title: 'Kit IoT DevKit dans le cloud : Connecter le kit IoT DevKit AZ3166 à l’accélérateur de solution IoT de surveillance à distance | Microsoft Docs'
description: Dans ce tutoriel, vous allez découvrir comment envoyer l’état des capteurs sur IoT DevKit AZ3166 à l’accélérateur de solution IoT de surveillance à distance à des fins de surveillance et de visualisation.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: e900b952ab9bb2054b9e4174670894027cdd2618
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969450"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Connecter MXChip IoT DevKit AZ3166 à l’accélérateur de solution de surveillance à distance IoT


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Dans ce tutoriel, vous allez apprendre à exécuter un exemple d’application sur votre kit DevKit pour envoyer les données des capteurs vers votre accélérateur de solution.

[IoT MXChip DevKit](https://aka.ms/iot-devkit) est une carte tout-en-un compatible Arduino qui est équipée de périphériques et de capteurs élaborés. Vous pouvez développer pour ce kit à l’aide de l’[extension Visual Studio Code pour Arduino](https://aka.ms/arduino). Par ailleurs, il s’accompagne d’un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en plein développement pour vous guider dans la réalisation de prototypes de solutions IoT (Internet des objets) qui exploitent les services Microsoft Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Suivez le [Guide de mise en route](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) pour :

* Connecter votre DevKit au Wi-Fi
* Préparer l’environnement de développement


## <a name="open-the-remotemonitoring-sample"></a>Ouvrir l’exemple RemoteMonitoring

1. Déconnectez le kit DevKit de votre ordinateur, s’il est connecté.

2. Démarrez VS Code.

3. Connectez le kit DevKit à votre ordinateur. VS Code détecte automatiquement votre kit DevKit et ouvre les pages suivantes :
  * La page d’introduction du kit DevKit.
  * Exemples Arduino : exemples pratiques pour démarrer avec le kit DevKit.

4. Développez la section **EXEMPLES ARDUINO** à gauche, accédez à **Exemples pour MXCHIP AZ3166 > AzureIoT** et sélectionnez **RemoteMonitoringv2**. Une nouvelle fenêtre VS Code s’ouvre avec le dossier de projet qu’elle contient.

  ![Ouvrir un projet de surveillance à distance](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > S’il vous arrive de fermer le volet par inadvertance, vous pouvez le rouvrir. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

## <a name="add-a-new-physical-device"></a>Ajouter un nouvel appareil physique

Dans le portail, accédez à la section **Appareils**, puis cliquez sur le bouton **+Nouvel appareil**. 

![Ajouter d’un nouvel appareil](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

Le *formulaire du nouvel appareil* doit être rempli.
1. Cliquez sur **Physique** dans la section*Type d’appareil*.
2. Définissez votre propre ID d’appareil (par exemple *MXChip* ou *AZ3166*).
3. Choisissez **Générer automatiquement les clés** dans la section *Clé d’authentification*.
4. Cliquez sur le bouton *Appliquer*.

![Formulaire d’ajout d’un nouvel appareil](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Attendez que le portail termine la configuration du nouvel appareil.

![Configurer un nouvel appareil ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


La configuration du nouvel appareil s’affichera.
Copiez la **chaîne de connexion** générée.

![Chaîne de connexion de l’appareil](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Cette chaîne de connexion sera utilisée dans la section suivante.





## <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

Revenez à Visual Studio Code : 

1. Utilisez `Ctrl+P` (macOS : `Cmd + P`) et tapez **task config-device-connection**.

  ![choisir votre abonnement Azure et votre IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Le terminal vous demande si vous souhaitez utiliser la chaîne de connexion de l’appareil IoT. Sélectionnez *Créer nouveau* et collez maintenant la chaîne de connexion.

  ![coller la chaîne de connexion](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Le terminal vous invite parfois à passer en mode de configuration. Pour cela, maintenez le bouton A enfoncée, appuyez puis relâchez le bouton de réinitialisation, puis relâchez le bouton A. L’écran affiche l’ID du kit DevKit et « Configuration ».

  ![Écran du kit DevKit de l’appareil](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Si vous avez suivi la dernière section de ce tutoriel, la chaîne de connexion devrait être enregistrée dans le Presse-papiers. Si ce n’est pas le cas, vous devez accéder au portail Azure et rechercher l’IoT Hub de votre groupe de ressources Surveillance à distance. Vous pouvez y consulter les appareils connectés IoT Hub et copier la chaîne de connexion de l’appareil.

  ![rechercher la chaîne de connexion](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Vous pouvez voir maintenant votre nouvel appareil physique dans la section VS Code « Appareils Azure IoT Hub » :

![Noter le nouvel appareil IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Tester le projet

Quand l’exemple d’application s’exécute, le kit DevKit envoie les données des capteurs par Wi-Fi à vos accélérateurs de solution IoT. Pour voir le résultat, procédez comme suit :

1. Accédez à votre accélérateur de solution IoT, puis cliquez sur **TABLEAU DE BORD**.

2. Dans la console de l’accélérateur de solution IoT, vous voyez l’état des capteurs de votre kit DevKit. 

![Données des capteurs dans les accélérateurs de solution IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Si vous cliquez sur le nom du capteur (AZ3166), un onglet s’ouvre à droite du tableau de bord, dans lequel vous pouvez consulter en temps réel le graphique des capteurs MX Chip.


## <a name="send-a-c2d-message"></a>Envoyer un message C2D
Remote Monitoring v2 vous permet d’appeler la méthode distante sur l’appareil.
L’exemple de code MX Chip publie trois méthodes que vous pouvez voir dans la section Méthode lorsque le capteur est sélectionné.

![Méthodes MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Vous pouvez modifier la couleur de l’une des LED MX Chip à l’aide de la méthode « LedColor ». Pour cela, activez la case à cocher de l’appareil, puis cliquez sur le bouton Planifier. 

![Méthodes MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Choisissez la méthode ChangeColor dans la liste déroulante répertoriant toutes les méthodes, écrivez un nom, puis sélectionnez Appliquer.

![Liste déroulante MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Après quelques secondes, votre MX Chip physique va modifier la couleur de la LED RVB (sous le bouton A)

![DEL MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>Modifier l’ID de l’appareil

Vous pouvez modifier l’ID de l’appareil dans IoT Hub en suivant [ce guide](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).


## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) si ou contactez-nous via les canaux ci-dessous :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un appareil DevKit à vos accélérateurs de solution IoT et à visualiser les données des capteurs, nous vous suggérons les étapes suivantes :

* [Vue d’ensemble des accélérateurs de solution IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connecter un appareil DevKit IoT MXChip à votre application Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
