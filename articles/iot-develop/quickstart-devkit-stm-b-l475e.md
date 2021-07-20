---
title: Connecter un appareil B-L475E-IOT01A ou B-L4S5I-IOT01A STMicroelectronics à Azure IoT Central – Démarrage rapide
description: Utilisez le logiciel intégré Azure RTOS pour connecter un appareil B-L475E-IOT01A ou B-L4S5I-IOT01A STMicroelectronics à Azure IoT et envoyer une télémétrie.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: dd8a214e060f9f4ace9f310fec3ebca33d4619c0
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109230"
---
# <a name="quickstart-connect-an-stmicroelectronics-b-l475e-iot01a-or-b-l4s5i-iot01a-discovery-kit-to-iot-central"></a>Démarrage rapide : Connecter un kit de découverte B-L475E-IOT01A ou B-L4S5I-IOT01A STMicroelectronics à Azure IoT Central

**S’applique à** : [Développement d’appareils intégrés](about-iot-develop.md#embedded-device-development)<br>
**Durée totale d’exécution** : 30 minutes

[![Parcourir le code](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+)

Ce démarrage rapide explique comment utiliser le logiciel Azure RTOS pour connecter le kit de découverte [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) ou [B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) STMicroelectronics (ci-après, le DevKit STM) à Azure IoT.

Vous allez effectuer les étapes suivantes :

* Installer un ensemble d’outils de développement intégrés pour la programmation du DevKit STM en C
* Créer une image et la flasher sur le DevKit STM
* Azure IoT Central permet de créer des composants cloud, d’afficher les propriétés, d’afficher la télémétrie des appareils et d’appeler des commandes directes

## <a name="prerequisites"></a>Prérequis

* PC exécutant Microsoft Windows 10
* [Git](https://git-scm.com/downloads) pour cloner le référentiel
* Matériel

    * Kit [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) ou [b-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) (DevKit STM)
    * Wi-Fi 2,4 GHz
    * USB 2.0, câble mâle-mâle micro USB

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Pour configurer votre environnement de développement, commencez par cloner un dépôt GitHub contenant toutes les ressources dont vous avez besoin pour le démarrage rapide. Ensuite, vous installez un ensemble d’outils de programmation.

### <a name="clone-the-repo-for-the-quickstart"></a>Cloner le dépôt pour le démarrage rapide

Clonez le référentiel suivant pour télécharger tous les exemples de code d’appareil, les scripts d’installation et les versions hors connexion de la documentation. Si vous avez déjà cloné ce dépôt dans le cadre d’un autre démarrage rapide, vous n’avez pas besoin de le refaire.

Pour cloner le référentiel, exécutez la commande suivante :

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Installer les outils

Le référentiel cloné contient un script d’installation qui installe et configure les outils requis. Si vous avez installé ces outils dans le cadre d’un autre démarrage rapide d’appareil intégré, vous n’avez pas besoin de le refaire.

> [!NOTE]
> Le script d’installation installe les outils suivants :
> * [CMake](https://cmake.org) : générer
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm) : compiler
> * [Termite](https://www.compuphase.com/software_termite.htm) : surveiller la sortie du port série pour les appareils connectés

Pour installer les outils :

1. Dans l’Explorateur de fichiers, accédez au chemin d’accès suivant dans le référentiel et exécutez le script d’installation nommé *get-toolchain.bat* :

    *getting-started\tools\get-toolchain.bat*

1. Après l’installation, ouvrez une nouvelle fenêtre de console pour identifier les modifications de configuration apportées par le script d’installation. Utilisez cette console pour accomplir les tâches de programmation restantes décrites dans ce démarrage rapide. Vous pouvez utiliser Windows CMD, PowerShell ou Git Bash pour Windows.
1. Exécutez le code suivant pour vérifier que CMake version 3.14 ou ultérieure est installé.

    ```shell
    cmake --version
    ```
[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour connecter le DevKit STM à Azure, vous allez modifier un fichier de configuration pour les paramètres Wi-Fi et Azure IoT, régénérer l’image et la flasher sur l’appareil.

### <a name="add-configuration"></a>Ajouter une configuration

1. Ouvrez le fichier suivant dans un éditeur de texte :

    *getting-started\STMicroelectronics\STM32L4_L4+\app\azure_config.h*

1. Affectez aux constantes Wi-Fi les valeurs suivantes à partir de votre environnement local.

    |Nom de la constante|Valeur|
    |-------------|-----|
    |`WIFI_SSID` |{*Votre SSID Wi-Fi*}|
    |`WIFI_PASSWORD` |{*Votre mot de passe Wi-Fi*}|
    |`WIFI_MODE` |{*Une des valeurs de mode de Wi-Fi énumérées dans le fichier*}|

1. Définissez les constantes d’informations de l’appareil Azure IoT sur les valeurs que vous avez enregistrées après avoir créé les ressources Azure.

    |Nom de la constante|Valeur|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Valeur d’étendue de votre ID*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Valeur d’ID de votre appareil*}|
    |`IOT_DEVICE_SAS_KEY` |{*Valeur de votre clé primaire*}|

1. Enregistrez et fermez le fichier.

### <a name="build-the-image"></a>Créer l’image

1. Dans votre console ou dans l’Explorateur de fichiers, exécutez le script *rebuild.bat* à l’emplacement suivant pour générer l’image :

    *getting-started\STMicroelectronics\STM32L4_L4+\tools\rebuild.bat*

2. Une fois la build terminée, vérifiez que deux fichiers binaires ont été créés. Il y a une image binaire pour chaque DevKit STM. La build enregistre les images dans les chemins d’accès suivants :

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l475_azure_iot.bin*

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l4S5_azure_iot.bin*

### <a name="flash-the-image"></a>Flasher l’image

1. Sur le MCU du DevKit STM, localisez le bouton **Réinitialiser**, le port USB Micro libellé **USB STLink** et le numéro de référence de la carte. Vous allez faire référence à ces éléments dans les étapes suivantes. Tous sont mis en évidence dans l’image suivante :

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/stm-devkit-board.png" alt-text="Localiser les composants clés sur la carte du DevKit STM":::

1. Connectez le câble USB du micro au port **USB STLINK** sur le DevKit STM, puis connectez celui-ci à votre ordinateur.

    > [!NOTE]
    > Pour obtenir des informations d’installation détaillées sur le DevKit STM, consultez les instructions figurant sur l’emballage, ou lisez [Ressources B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html#resource) ou [Ressources B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html#resource).

1. Dans l’Explorateur de fichiers, recherchez les fichier binaires que vous avez créés dans la section précédente.

1. Copiez le fichier binaire dont le nom correspond au numéro de référence du DevKit STM que vous utilisez. Par exemple, si le numéro de référence de votre carte est **B-L475E-IOT01A1**, copiez le fichier binaire nommé *stm32l475_azure_iot.bin*.

1. Dans l’Explorateur de fichiers, recherchez le DevKit STM connecté à votre ordinateur. L’appareil apparaît en tant que lecteur sur votre système avec le nom de lecteur **DIS_L4IOT**.

1. Collez le fichier binaire dans le dossier racine du DevKit STM. Le processus de flash démarre automatiquement et se termine en quelques secondes.

    > [!NOTE]
    > Au cours du processus de flashage, un témoin LED alterne entre le rouge et le vert sur le DevKit STM.

### <a name="confirm-device-connection-details"></a>Vérifier les détails de connexion de l’appareil

Vous pouvez utiliser l’application **Termite** pour surveiller la communication et vérifier que votre appareil est correctement configuré.

1. Démarrez **Termite**.
    > [!TIP]
    > Si vous ne parvenez pas à connecter Termite à votre devkit, installez le [pilote ST-LINK](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip), puis réessayez. Pour obtenir des étapes supplémentaires, consultez [Résolution des problèmes](troubleshoot-embedded-device-quickstarts.md).
1. Sélectionnez **Paramètres**.
1. Dans la boîte de dialogue **Paramètres du port série**, vérifiez les paramètres suivants et mettez à jour si nécessaire :
    * **Vitesse (en bauds)**  : 115 200
    * **Port** : port auquel votre DevKit STM est connecté. Si plusieurs options de port sont disponibles dans la liste déroulante, vous pouvez trouver le port approprié à utiliser. Ouvrez le **Gestionnaire d'appareils** Windows et affichez les **Ports** pour identifier le port à utiliser.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/termite-settings.png" alt-text="Capture d’écran des paramètres de port série dans l’application Termite":::

1. Sélectionnez OK.
1. Appuyez sur le bouton **Réinitialiser** de l’appareil. Le bouton est noir et libellé sur l’appareil.
1. Dans l’application **Termite**, vérifiez les valeurs de point de contrôle suivantes pour confirmer que l’appareil est initialisé et connecté à Azure IoT.

    ```output
    Starting Azure thread

    Initializing WiFi
        Module: ISM43362-M3G-L44-SPI
        MAC address: C4:7F:51:8F:67:F6
        Firmware revision: C3.5.2.5.STM
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.22
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 108.62.122.57
        SNTP time update: May 21, 2021 22:42:8.394 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsg;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```
    > [!IMPORTANT]
    > Si l’initialisation du client DNS échoue et vous avertit que le microprogramme Wi-Fi est obsolète, vous devez mettre à jour le microprogramme du module Wi-Fi. Téléchargez et installez la [mise à jour du microprogramme du module Wi-Fi ISM 43362 Inventek](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip). Appuyez ensuite sur le bouton **Réinitialiser** de l’appareil pour revérifier votre connexion, puis poursuivez avec ce démarrage rapide.


Laissez Termite ouvert pour surveiller la sortie de l’appareil dans les étapes suivantes.

## <a name="verify-the-device-status"></a>Vérifier l’état de l’appareil

Pour consulter l’état de l’appareil dans le portail IoT Central :
1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Vérifiez que l’**état de l'appareil** est mis à jour en **approvisionné**.
1. Vérifiez que le **modèle d’appareil** est mis à jour en **Guide de prise en main**.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-view-status.png" alt-text="Capture d’écran de l’état de l’appareil dans IoT Central":::

## <a name="view-telemetry"></a>Afficher les données de télémétrie

Avec IoT Central, vous pouvez afficher le flux de télémétrie de votre appareil dans le cloud.

Pour afficher la télémétrie dans le portail IoT Central :

1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Sélectionnez l’appareil dans la liste.
1. Affichez la télémétrie lorsque l’appareil envoie des messages au cloud sous l’onglet **Vue d’ensemble**.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-telemetry.png" alt-text="Capture d’écran de la télémétrie de l’appareil dans IoT Central":::

    > [!NOTE]
    > Vous pouvez également surveiller la télémétrie à partir de l’appareil à l’aide de l’application Termite.

## <a name="call-a-direct-method-on-the-device"></a>Appeler une méthode directe sur l’appareil

Vous pouvez également utiliser IoT Central pour appeler une méthode directe que vous avez implémentée sur votre appareil. Les méthodes directes portent un nom et peuvent éventuellement avoir une charge utile JSON, une connexion configurable et un délai d’expiration de méthode. Dans cette section, vous appelez une méthode qui vous permet d’activer ou de désactiver un voyant.

Pour appeler une méthode dans le portail IoT Central :

1. Sélectionnez l’onglet **Commande** sur la page de l’appareil.
1. Dans la liste déroulante **État**, sélectionnez **Vrai**, puis **Exécuter**.  Le voyant doit s’allumer.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-invoke-method.png" alt-text="Capture d’écran de l’appel d’une méthode directe sur un appareil dans IoT Central":::

1. Dans la liste déroulante **État**, sélectionnez **Faux**, puis **Exécuter**. Le voyant doit s’éteindre.

## <a name="view-device-information"></a>Affichage des informations sur l’appareil

Vous pouvez afficher les informations de l’appareil à partir d’IoT Central.

Sélectionnez l’onglet **À propos de** sur la page de l’appareil.

:::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-about.png" alt-text="Capture d’écran des informations sur l’appareil dans IoT Central":::

## <a name="troubleshoot-and-debug"></a>Résoudre les problèmes et déboguer

Si vous rencontrez des problèmes pour générer le code d’appareil, flasher l’appareil ou le connecter, consultez [Résolution des problèmes](troubleshoot-embedded-device-quickstarts.md).

Pour déboguer l’application, consultez [Débogage avec Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources Azure créées dans le cadre de ce démarrage rapide, vous pouvez les supprimer du portail IoT Central.

Pour supprimer la totalité de l’exemple d’application Azure IoT Central et tous ses appareils et ressources :
1. Sélectionnez **Administration** > **Votre application**.
1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce démarrage rapide, vous avez créé une image personnalisée contenant un exemple de code Azure RTOS, puis flashé l’image sur l’appareil DevKit STM. Vous avez également utilisé le portail IoT Central pour créer des ressources Azure, connecter le DevKit STM en toute sécurité à Azure, afficher la télémétrie et envoyer des messages.

L’étape suivante consiste à explorer les articles ci-dessous pour savoir comment connecter des appareils à Azure IoT à l’aide des kits de développement logiciel (SDK) d’appareil IoT. 

> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Hub](quickstart-send-telemetry-iot-hub.md)


> [!IMPORTANT]
> Azure RTOS fournit aux fabricants OEM les composants permettant de sécuriser la communication et de créer du code et l’isolation des données à l’aide des mécanismes sous-jacents de protection matérielle MCU/MPU. Toutefois, chaque fabricant OEM est tenu de garantir que son appareil répond aux exigences de sécurité en constante évolution.



