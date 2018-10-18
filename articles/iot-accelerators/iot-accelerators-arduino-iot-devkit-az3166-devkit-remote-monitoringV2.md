---
title: Kit IoT DevKit dans le cloud - Connecter le kit IoT DevKit AZ3166 à l’accélérateur de solution IoT de supervision à distance | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment envoyer l’état des capteurs sur IoT DevKit AZ3166 à l’accélérateur de solution IoT de surveillance à distance à des fins de surveillance et de visualisation.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720580"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Connecter MXChip IoT DevKit AZ3166 à l’accélérateur de solution de surveillance à distance IoT

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Vous allez apprendre à exécuter un exemple d’application sur votre kit DevKit IoT pour envoyer les données des capteurs vers votre accélérateur de solution.

[IoT MXChip DevKit](https://aka.ms/iot-devkit) est une carte tout-en-un compatible Arduino qui est équipée de périphériques et de capteurs élaborés. Vous pouvez développer pour ce kit à l’aide d’[Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) dans Visual Studio Code. Par ailleurs, il s’accompagne d’un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en plein développement pour vous guider dans la réalisation de prototypes de solutions IoT (Internet des objets) qui exploitent les services Microsoft Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Passez en revue le [Guide de mise en route](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) et **ne terminez que les sections suivantes** :

* Préparation du matériel
* Configurer le Wi-Fi
* Commencer à utiliser le kit DevKit
* Préparer l’environnement de développement

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Ouvrir l’exemple de supervision à distance dans VS Code

1. Assurez-vous que votre IoT DevKit n’est **pas connecté** à votre ordinateur. Démarrez d’abord VS Code, puis connectez le DevKit à votre ordinateur.

2. Cliquez sur `F1` pour ouvrir la palette de commandes, tapez et sélectionnez **IoT Workbench : Exemples**. Puis sélectionnez **IoT DevKit** en tant que carte.

3. Cherchez **Supervision à distance** et cliquez sur **Ouvrir l’exemple**. Une nouvelle fenêtre VS Code s’ouvre avec le dossier de projet qu’elle contient.
  ![IoT Workbench : sélectionnez l’exemple de supervision à distance](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>Configurer la chaîne de connexion d’appareil IoT Hub

1. Passez le IoT DevKit en **mode de configuration**. Pour ce faire :
   * Maintenez enfoncé le bouton **A**.
   * Enfoncez et relâchez le bouton **Réinitialiser**.

2. L’écran affiche l’ID du kit DevKit et « Configuration ».
   
  ![Mode de configuration du IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. Cliquez sur `F1` pour ouvrir la palette de commandes, tapez et sélectionnez **IoT Workbench : Appareil > Config Device Settings** (Configurer les paramètres de l’appareil).

4. Collez la chaîne de connexion que vous venez de copier et cliquez sur `Enter` pour la configurer.

## <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

1. Cliquez sur `F1` pour ouvrir la palette de commandes, tapez et sélectionnez **IoT Workbench : Appareil > Device Upload** (Chargement d’appareil).
  ![IoT Workbench : Appareil - > Charger](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code lance ensuite la compilation et le chargement du code sur votre DevKit.
  ![IoT Workbench : Appareil - > Téléchargé](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

Le DevKit redémarre et commence à exécuter le code.

## <a name="test-the-project"></a>Tester le projet

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Afficher les données de télémétrie envoyées à la solution de supervision à distance

Quand l’exemple d’application s’exécute, le kit DevKit envoie les données des capteurs par Wi-Fi à votre solution de supervision à distance. Pour voir le résultat, procédez comme suit :

1. Accédez au tableau de bord de votre solution, puis cliquez sur **Appareils**.

2. Cliquez sur le nom de l’appareil. Sous l’onglet de droite, vous pouvez voir l’état du capteur sur le DevKit en temps réel.
  ![Données des capteurs dans Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>Envoyer un message C2D

La solution de supervision à distance vous permet d’appeler la méthode distante sur l’appareil. L’exemple de code permet de publier trois méthodes que vous pouvez voir dans la section **Méthode** lorsque le capteur est sélectionné.

![Méthodes IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Essayez de modifier la couleur de l’une des LED du DevKip à l’aide de la méthode « LedColor ».

1. Sélectionnez le nom de l’appareil dans la liste des appareils et cliquez sur **Travaux**.

  ![Création d’un travail](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. Configurez les travaux comme indiqué ci-dessous, puis cliquez sur **Appliquer**.
  * Sélectionner un travail : **Exécuter une méthode**
  * Nom de la méthode : **LedColor**
  * Nom du travail : **ChangeLedColor**
  
  ![Paramètres d’un travail](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

Après quelques secondes, votre DevKit va modifier la couleur de la LED RVB (sous le bouton A).

![LED rouge de l’IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de passer aux tutoriels, laissez l’accélérateur de solution Monitoring à distance déployé.

Si vous n’avez plus besoin de l’accélérateur de solution, supprimez-le de la page Solutions approvisionnées en le sélectionnant et en cliquant sur Supprimer la solution :

![Supprimer la solution](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez les [FAQ sur le DevKit IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contactez-nous par le biais des canaux suivants :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à connecter un appareil DevKit à votre accélérateur de solution de surveillance à distance Azure IoT et à visualiser les données des capteurs ; nous vous suggérons à présent les étapes suivantes :

* [Vue d’ensemble des accélérateurs de solution Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Personnaliser l’interface utilisateur](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Connecter IoT DevKit à votre application Azure IoT Central](../iot-central/howto-connect-devkit.md)