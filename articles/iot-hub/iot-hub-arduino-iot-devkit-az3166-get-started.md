---
title: Kit IoT DevKit vers cloud - Connecter le kit IoT DevKit AZ3166 à Azure IoT Hub | Microsoft Docs
description: Dans ce didacticiel, découvrez comment configurer le kit IoT DevKit AZ3166 et le connecter à Azure IoT Hub pour qu’il puisse envoyer des données sur la plateforme cloud Azure.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: rangv
ms.openlocfilehash: 360937d335eadb6d235eb52c0d7df42f896a0de0
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344729"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Connecter IoT DevKit AZ3166 à Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Vous pouvez utiliser le kit [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) pour développer et réaliser des prototypes de solutions IoT (Internet of Things) qui tirent parti des services Microsoft Azure. Il comprend une carte compatible Arduino avec un grand choix de périphériques et de capteurs élaborés, un package de carte open source et un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) de plus en plus riche.

## <a name="what-you-do"></a>Procédure

Connectez le kit [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) à un hub Azure IoT que vous créez, collectez les données de température et d’humidité à partir des capteurs et envoyez-les au hub IoT.

Vous n’avez pas encore de DevKit ? Essayez le [simulateur DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [achetez un DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Contenu

* Comment connecter le kit IoT DevKit à un point d’accès sans fil et préparer votre environnement de développement.
* Comment créer un hub IoT et enregistrer un appareil auprès du kit MXChip IoT DevKit.
* Comment collecter les données des capteurs en exécutant un exemple d’application sur le kit MXChip IoT DevKit.
* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Une carte MXChip IoT DevKit avec câble micro-USB. [Achetez-en une maintenant](https://aka.ms/iot-devkit-purchase).
* Un ordinateur exécutant Windows 10 ou macOS 10.10+.
* Un abonnement Azure actif. [Activez un compte Microsoft Azure pour un essai gratuit de 30 jours](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Préparation du matériel

Raccordez le matériel suivant à votre ordinateur :

* Carte DevKit
* Câble micro-USB

![Matériel requis](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Pour brancher le kit DevKit sur votre ordinateur, procédez comme suit :

1. Branchez l’extrémité USB sur l’ordinateur.

2. Branchez la fiche micro-USB sur le kit DevKit.

3. Le témoin vert de l’alimentation confirme le branchement.

   ![Branchements du matériel](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurer le Wi-Fi

Les projets IoT sont tributaires de la connectivité Internet. Utilisez les instructions suivantes pour configurer la connexion du kit DevKit au Wi-Fi.

### <a name="enter-ap-mode"></a>Activer le mode « point d’accès »

Maintenez le bouton B enfoncé, appuyez sur le bouton de réinitialisation (Reset) et relâchez-le, puis relâchez le bouton B. Votre kit DevKit passe en mode « point d’accès » pour la configuration du Wi-Fi. L’écran affiche l’identificateur SSID (Service Set Identifier) du kit DevKit et l’adresse IP du portail de configuration.

![Bouton de réinitialisation, bouton B et SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Se connecter au point d’accès DevKit

Utilisez à présent un autre appareil compatible Wi-Fi (un téléphone mobile ou un ordinateur) pour vous connecter au SSID du kit DevKit (mis en évidence dans l’image précédente). Laissez le mot de passe vide.

![Bouton de connexion et informations réseau](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurer le Wi-Fi du kit DevKit

Ouvrez l’adresse IP affichée sur l’écran du kit DevKit dans le navigateur de votre ordinateur ou de votre téléphone mobile, sélectionnez le réseau Wi-Fi auquel vous souhaitez que le kit DevKit se connecte et tapez le mot de passe. Sélectionnez **Connecter**.

![Zone de mot de passe et bouton de connexion](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Dès que la connexion est établie, le kit DevKit redémarre au bout de quelques secondes. L’adresse IP et le nom du Wi-Fi apparaissent alors à l’écran :

![Nom Wi-Fi et adresse IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> L’adresse IP présentée sur la photo peut ne pas correspondre à l’adresse IP réellement attribuée et affichée sur l’écran du kit DevKit. Cette divergence est normale dans la mesure où le Wi-Fi utilise le DHCP pour attribuer dynamiquement les adresses IP.

Dès lors que le Wi-Fi est configuré, vos informations d’identification pour cette connexion sont persistantes sur l’appareil, même s’il est débranché. Par exemple, si vous configurez le kit DevKit pour le Wi-Fi à votre domicile, et que vous l’emportez ensuite au bureau, vous devez reconfigurer le mode « point d’accès » (en commençant à l’étape Activer le mode « point d’accès ») pour connecter le kit DevKit au Wi-Fi de votre bureau. 

## <a name="start-using-the-devkit"></a>Commencer à utiliser le kit DevKit

L’application qui s’exécute par défaut sur le kit DevKit vérifie la dernière version du microprogramme installée et affiche certaines données de diagnostic des capteurs.

### <a name="upgrade-to-the-latest-firmware"></a>Passer à la dernière version du microprogramme

> [!NOTE]
> Depuis la version 1.1, le kit DevKit active ST-SAFE dans le chargeur de démarrage. Vous devez mettre à niveau le microprogramme si vous exécutez une version antérieure à la v1.1.

Si une mise à niveau du microprogramme est nécessaire, l’écran affiche la version actuelle et la version la plus récente du microprogramme. Pour la mise à niveau, suivez le guide [Mettre à niveau le microprogramme](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).

![Affichage de la versions actuelle et de la version plus récente du microprogramme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Cette opération n’est nécessaire qu’une seule fois. Dès que vous commencez à développer sur le kit DevKit et que vous chargez votre application, la dernière version du microprogramme est fournie avec votre application.

### <a name="test-various-sensors"></a>Tester divers capteurs

Appuyez sur le bouton B pour tester les capteurs. Continuez d’appuyer sur ce bouton et de relâcher votre pression à chaque fois pour passer en revue tous les capteurs.

![Affichage d’un capteur et du bouton B](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

### <a name="install-azure-iot-workbench"></a>Installer Azure IoT Workbench

Nous recommandons l’extension [Azure IoT Workbench](https://aka.ms/iot-workbench) pour Visual Studio Code afin de procéder au développement sur le DevKit.

Azure IoT Workbench fournit une expérience intégrée pour développer des solutions IoT. Il contribue au développement sur l’appareil et le cloud à l’aide d’Azure IoT et d’autres services. Vous pouvez regarder cette vidéo de Channel9 pour obtenir une vue d’ensemble de son fonctionnement.

Suivez ces étapes pour préparer l’environnement de développement pour DevKit :

1. Téléchargez et installez [Arduino IDE](https://www.arduino.cc/en/Main/Software). Il fournit la chaîne d’outils nécessaire pour la compilation et le chargement du code Arduino.
    * **Windows** : utilisez la version Windows Installer.
    * **macOS** : faites glisser et déposez le fichier **Arduino.app** extrait dans le dossier `/Applications`.
    * **Ubuntu** : décompressez-le dans un dossier, tel que `$HOME/Downloads/arduino-1.8.5`

1. Installez [Visual Studio Code](https://code.visualstudio.com/), un éditeur de code source inter-plateformes avec des outils puissants pour les développeurs, comme la complétion et le débogage de code IntelliSense.

1. Recherchez **Azure IoT Workbench** sur la Place de marché des extensions et installez-le.
    ![Installez Azure IoT Workbench](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png) avec IoT Workbench, les autres extensions dépendantes seront installées.

1. Ouvrez **Fichier > Préférences > Paramètres** et ajoutez les lignes suivantes pour configurer Arduino.
    * **Windows** :
    ```javascript
    "arduino.path": "C:\\Program Files (x86)\\Arduino",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **macOS** :
    ```javascript
    "arduino.path": "/Applications",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **Ubuntu** :
    ```javascript
    "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

1. Cliquez sur `F1` pour ouvrir la palette de commandes, tapez et sélectionnez **Arduino : Gestionnaire de cartes**. Recherchez **AZ3166** et installez la dernière version.
    ![Installer le kit SDK DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>Installer les pilotes ST-Link

[ST-Link/V2](http://www.st.com/en/development-tools/st-link-v2.html) est l’interface USB utilisée par IoT DevKit pour communiquer avec votre ordinateur de développement. Suivez les étapes spécifiques au système d’exploitation pour permettre à l’ordinateur d’accéder à votre appareil.

* **Windows** : téléchargez et installez le pilote USB à partir du [site web STMicroelectronics](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS** : aucun pilote n’est requis pour macOS.
* **Ubuntu** : exécutez la commande suivante dans le terminal, déconnectez-vous et connectez-vous pour que la modification du groupe prenne effet :
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Maintenant, vous êtes prêt à préparer et à configurer votre environnement de développement. Nous allons générer l’exemple « Hello World » pour IoT : envoyer des données de télémétrie de température à Azure IoT Hub.

## <a name="build-your-first-project"></a>Générer votre premier projet

1. Assurez-vous que votre IoT DevKit n’est **pas connecté** à votre ordinateur. Démarrez d’abord VS Code, puis connectez le DevKit à votre ordinateur.

1. Dans la barre d’état en bas à droite, vérifiez que **MXCHIP AZ3166** est indiqué en tant que carte sélectionnée et que le port série avec **STMicroelectronics** est utilisé.
    ![Sélectionner la carte et COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Cliquez sur `F1` pour ouvrir la palette de commandes, tapez et sélectionnez **IoT Workbench : Exemples**. Puis sélectionnez **IoT DevKit** en tant que carte.

1. Dans la page des exemples IoT Workbench, recherchez **Bien démarrer** et cliquez sur **Ouvrir l’exemple**. Sélectionnez ensuite le chemin d’accès par défaut pour télécharger l’exemple de code.
    ![Ouvrir l’exemple](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Si vous n’avez pas installé l’extension Arduino dans VS Code, cliquez sur **Installer** dans le volet de notification.
    ![Installer l’extension Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. Dans la nouvelle fenêtre de projet ouverte, cliquez sur `F1` pour ouvrir la palette de commandes, tapez et sélectionnez **IoT Workbench : Cloud**, puis sélectionnez **Azure Provision**. Suivez le guide détaillé pour terminer le provisionnement de votre Azure IoT Hub et créer l’appareil.
    ![Provisionnement du cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Cliquez sur `F1` pour ouvrir la palette de commandes, tapez et sélectionnez **IoT Workbench : Appareil**, puis sélectionnez **Configurer les paramètres de l’appareil > Sélectionner la chaîne de connexion d’appareil IoT Hub**.

1. Dans DevKit, maintenez le **bouton A** enfoncé, appuyez sur le bouton de **réinitialisation** et relâchez-le, puis relâchez le **bouton A**. Votre DevKit passe en mode de configuration et enregistre la chaîne de connexion.
    ![Chaîne de connexion](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Cliquez de nouveau sur `F1`, tapez et sélectionnez **IoT Workbench : Appareil**, puis sélectionnez **Chargement d’un appareil**.
    ![Chargement Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Le DevKit redémarre et commence à exécuter le code.

> [!NOTE]
> En cas d’erreurs ou d’interruptions, vous pouvez toujours récupérer en exécutant la commande à nouveau.

## <a name="test-the-project"></a>Tester le projet

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Afficher les données de télémétrie envoyées à Azure IoT Hub

Cliquez sur l’icône de branchement « power » dans la barre d’état pour ouvrir l’analyse en série : ![Serial monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

L’exemple d’application s’exécute correctement si les résultats suivants s’affichent :

* Serial Monitor affiche le message envoyé à IoT Hub.
* Le témoin lumineux du kit MXChip IoT DevKit clignote.

![Sortie de Serial Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Afficher les données de télémétrie reçues par Azure IoT Hub

Vous pouvez utiliser [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) pour superviser les messages appareil-à-cloud (D2C) dans IoT Hub.

1. Dans Visual Studio Code, recherchez **Azure IoT Toolkit** dans la Place de marché des extensions et installez-le.

1. Connectez-vous au [portail Azure](https://portal.azure.com/), recherchez l’IoT Hub que vous avez créé.
    ![Portail Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Dans le volet **Stratégies d’accès partagé**, cliquez sur la **stratégie iothubowner** et notez la chaîne de connexion de votre IoT Hub.
    ![Chaîne de connexion de l’IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Dans Visual Studio Code, développez **APPAREILS AZURE IOT HUB** dans le coin inférieur gauche, cliquez sur **Définir la chaîne de connexion IoT Hub**.
    ![Définir la chaîne de connexion IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Cliquez sur **IoT : Démarrer l’analyse du message D2C** dans le menu contextuel.

1. Dans le volet **SORTIE**, les messages appareil-à-cloud entrants vers l’IoT Hub s’affichent.
    ![Messages appareil-à-cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, vous pouvez rechercher une solution dans la [FAQ de l’IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contactez-nous à partir de [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Vous pouvez également nous faire part de vos remarques en laissant un commentaire sur cette page.

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté un kit MXChip IoT DevKit à votre hub IoT et avez envoyé des données de capteur collectées à votre hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
