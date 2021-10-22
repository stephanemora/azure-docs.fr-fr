---
title: Démarrage rapide - Connecter un Microchip ATSAME54-XPro à Azure IoT Central
description: Utilisez le logiciel embarqué Azure RTOS pour connecter un Microchip ATSAME54-XPro à Azure IoT et envoyer la télémétrie.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 10/18/2021
zone_pivot_groups: iot-develop-toolset
ms.openlocfilehash: 2b2b0cdb4c81a8ca501c58fce9ddb4771a5bbaba
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167597"
---
# <a name="quickstart-connect-a-microchip-atsame54-xpro-evaluation-kit-to-iot-central"></a>Démarrage rapide : connecter un kit d’évaluation Microchip ATSAME54-XPro à IoT Central

**S’applique à** : [Développement d’appareils embarqués](about-iot-develop.md#embedded-device-development)<br>
**Durée totale d’exécution** : 45 minutes

:::zone pivot="iot-toolset-cmake"
[![Parcourir le code](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO)
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
[![Parcourir le code](media/common/browse-code.svg)](https://github.com/azure-rtos/samples/)
:::zone-end

Dans ce guide de démarrage rapide, vous utilisez Azure RTOS pour connecter le kit Microchip ATSAME54-XPro (dorénavant, le Microchip E54) à Azure IoT.

Vous allez effectuer les étapes suivantes :

* Installer un ensemble d’outils de développement embarqués pour programmer un Microchip E54 en C
* Créer une image et la flasher sur le Microchip E54
* Azure IoT Central permet de créer des composants cloud, d’afficher les propriétés, d’afficher la télémétrie des appareils et d’appeler des commandes directes

:::zone pivot="iot-toolset-cmake"

## <a name="prerequisites"></a>Prérequis

* PC exécutant Microsoft Windows 10
* [Git](https://git-scm.com/downloads) pour cloner le référentiel
* Matériel

  * [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * USB 2.0, câble mâle-mâle micro USB
  * Accès Ethernet câblé
  * Câble Ethernet
  * Facultatif : Capteur [Weather Click](https://www.mikroe.com/weather-click). Vous pouvez ajouter ce capteur à l’appareil pour surveiller les conditions météorologiques. Si vous n’avez pas ce capteur, vous pouvez quand même suivre ce guide de démarrage rapide.
  * Facultatif : Adaptateur [mikroBUS Xplained Pro](https://www.microchip.com/Developmenttools/ProductDetails/ATMBUSADAPTER-XPRO). Utilisez cet adaptateur pour attacher le capteur Weather Click au Microchip E54. Si vous n’avez ni le capteur ni cet adaptateur, vous pouvez quand même suivre ce guide de démarrage rapide.

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
>
> * [CMake](https://cmake.org) : générer
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm) : compiler
> * [Termite](https://www.compuphase.com/software_termite.htm) : surveiller la sortie du port série pour les appareils connectés

Pour installer les outils :

1. Dans l’Explorateur de fichiers, accédez au chemin d’accès suivant dans le référentiel et exécutez le script d’installation nommé ***get-toolchain.bat*** :

    *getting-started\tools\get-toolchain.bat*

1. Après l’installation, ouvrez une nouvelle fenêtre de console pour identifier les modifications de configuration apportées par le script d’installation. Utilisez cette console pour accomplir les tâches de programmation restantes décrites dans ce démarrage rapide. Vous pouvez utiliser Windows CMD, PowerShell ou Git Bash pour Windows.
1. Exécutez le code suivant pour vérifier que CMake version 3.14 ou ultérieure est installé.

    ```shell
    cmake --version
    ```

1. Installez [Microchip Studio for AVR&reg; et les appareils SAM](https://www.microchip.com/en-us/development-tools-tools-and-software/microchip-studio-for-avr-and-sam-devices#). Microchip Studio est un environnement de développement d’appareils qui comprend les outils permettant de programmer et de flasher le Microchip E54. Pour ce tutoriel, vous utilisez Microchip Studio uniquement pour flasher le Microship E54. L’installation prend plusieurs minutes et vous invite plusieurs fois à approuver l’installation des composants.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour connecter le Microchip E54 à Azure, vous allez modifier un fichier de configuration pour les paramètres Azure IoT, regénérer l’image et la flasher sur l’appareil.

### <a name="add-configuration"></a>Ajouter une configuration

1. Ouvrez le fichier suivant dans un éditeur de texte :

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Définissez les constantes d’informations de l’appareil Azure IoT sur les valeurs que vous avez enregistrées après avoir créé les ressources Azure.

    |Nom de la constante|Valeur|
    |-------------|-----|
    | `IOT_DPS_ID_SCOPE` | {*Valeur d’étendue de votre ID*} |
    | `IOT_DPS_REGISTRATION_ID` | {*Valeur d’ID de votre appareil*} |
    | `IOT_DEVICE_SAS_KEY` | {*Valeur de votre clé primaire*} |

1. Enregistrez et fermez le fichier.

### <a name="connect-the-device"></a>Connecter l’appareil

1. Sur le Microchip E54, localisez le bouton **Reset**, le port **Ethernet** et le port Micro USB qui s’appelle **Debug USB**. Chaque composant est mis en évidence dans l’image suivante :

    ![Localiser les composants clés sur le tableau du kit d’évaluation du Microchip E54](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. Branchez le câble Micro USB au port **Debug USB** du Microchip E54, puis branchez-le à votre ordinateur.

    > [!NOTE]
    > Pour plus d’informations sur la configuration et la prise en main du Microchip E54, consultez le [Guide de l’utilisateur sur SAM E54 Xplained Pro](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf) si vous le souhaitez.

1. Utilisez le câble Ethernet pour connecter le Microchip E54 à un port Ethernet.

### <a name="optional-install-a-weather-sensor"></a>Facultatif : Installer un capteur météo

Si vous avez le capteur Weather Click et l’adaptateur mikroBUS Xplained Pro, suivez les étapes de cette section. Sinon, passez à [Générer l’image](#build-the-image). Vous pouvez suivre ce guide de démarrage rapide même si vous n’avez pas de capteur. L’exemple de code pour l’appareil retourne des données simulées si aucun capteur réel n’est présent.

1. Si vous avez le capteur Weather Click et l’adaptateur mikroBUS Xplained Pro, installez-les sur le Microchip E54 comme illustré sur la photo suivante :

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/sam-e54-sensor.png" alt-text="Installer le capteur Weather Click et l’adaptateur mikroBUS Xplained Pro sur le Microchip E54":::

1. Rouvrez le fichier de configuration que vous avez modifié précédemment :

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Définissez la valeur de la constante `__SENSOR_BME280__` sur **1** comme indiqué dans le code suivant du fichier d’en-tête. La définition de cette valeur permet à l’appareil d’utiliser de vraies données du capteur Weather Click.

    `#define __SENSOR_BME280__ 1`

1. Enregistrez et fermez le fichier.

### <a name="build-the-image"></a>Créer l’image

1. Dans votre console ou dans l’Explorateur de fichiers, exécutez le script ***rebuild.bat*** à l’emplacement suivant pour générer l’image :

    *getting-started\Microchip\ATSAME54-XPRO\tools\rebuild.bat*

1. Une fois la génération terminée, vérifiez que le fichier binaire a été créé dans le chemin d’accès suivant :

    *getting-started\Microchip\ATSAME54-XPRO\build\app\atsame54_azure_iot.bin*

### <a name="flash-the-image"></a>Flasher l’image

1. Ouvrez la console **Démarrer Windows > Microchip Studio** à l’invite de commandes et accédez au dossier du fichier binaire Microchip E54 que vous avez créé.

    *getting-started\Microchip\ATSAME54-XPRO\build\app*

1. Servez-vous de l’utilitaire *atprogram* pour flasher le Microchip E54 avec l’image binaire :

    ```shell
    atprogram --tool edbg --interface SWD --device ATSAME54P20A program --chiperase --file atsame54_azure_iot.bin --verify
    ```

    > [!NOTE]
    > Pour plus d’informations sur l’utilisation des outils Atmel-ICE et atprogram avec le Microchip E54, consultez [Using Atmel-ICE for AVR Programming In Mass Production](http://ww1.microchip.com/downloads/en/AppNotes/00002466A.pdf).

    Une fois le processus de flash terminé, la console confirme la réussite de la programmation :

    ```output
    Firmware check OK
    Programming and verification completed successfully.
    ```

### <a name="confirm-device-connection-details"></a>Vérifier les détails de connexion de l’appareil

Vous pouvez utiliser l’application **Termite** pour surveiller la communication et vérifier que votre appareil est correctement configuré.

1. Démarrez **Termite**.

    > [!TIP]
    > Si vous rencontrez des problèmes lors de l’initialisation ou de la connexion de votre appareil après la mise à jour, consultez Résolution des problèmes](troubleshoot-embedded-device-quickstarts.md) pour obtenir des étapes supplémentaires.

1. Sélectionnez **Paramètres**.

1. Dans la boîte de dialogue **Paramètres du port série**, vérifiez les paramètres suivants et mettez à jour si nécessaire :
    * **Vitesse (en bauds)**  : 115 200
    * **Port** : port auquel votre Microchip E54 est connecté. Si plusieurs options de port sont disponibles dans la liste déroulante, vous pouvez trouver le port approprié à utiliser. Ouvrez le **Gestionnaire d'appareils** Windows et affichez les **Ports** pour identifier le port à utiliser.
    * **Contrôle de flux** : DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Capture d’écran des paramètres du port série dans l’application Termite":::

1. Sélectionnez **OK**.

1. Appuyez sur le bouton **Réinitialiser** de l’appareil. Le bouton est étiqueté sur l’appareil et situé près du connecteur micro USB.

1. Dans l’application **Termite**, confirmez les valeurs de point de contrôle suivantes pour vérifier que l’appareil est initialisé et connecté à Azure IoT.

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 192.168.0.21
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 45.55.58.103
        SNTP time update: Jun 5, 2021 20:2:46.32 UTC 
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

Laissez Termite ouvert pour surveiller la sortie de l’appareil dans les étapes suivantes.

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"

## <a name="prerequisites"></a>Prérequis

* PC exécutant Microsoft Windows 10

* Matériel

  * [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * USB 2.0, câble mâle-mâle micro USB
  * Accès Ethernet câblé
  * Câble Ethernet

* [Termite](https://www.compuphase.com/software_termite.htm). Dans la page web, sous **Downloads and license**, choisissez l’installation complète. Termite est un terminal RS232 que vous utiliserez pour superviser la sortie de votre appareil.

* IAR Embedded Workbench pour ARM (EW pour ARM). Vous pouvez télécharger et installer un [essai gratuit de 14 jours d’IAR EW pour ARM](https://www.iar.com/products/architectures/arm/iar-embedded-workbench-for-arm/).

* Téléchargez le fichier [Azure_RTOS_6.1_ATSAME54-XPRO_IAR_Samples_2020_10_10.zip](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_IAR_Samples_2020_10_10.zip) et extrayez-le dans un répertoire de travail. Choisissez un répertoire avec un chemin bref pour éviter les erreurs du compilateur lors de la génération.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour connecter le Microchip E54 à Azure, vous allez connecter le Microchip E54 à voter ordinateur, modifier un fichier de configuration pour les paramètres Azure IoT, générer l’image et la flasher sur l’appareil.

### <a name="connect-the-device"></a>Connecter l’appareil

1. Sur le Microchip E54, localisez le bouton **Reset**, le port **Ethernet** et le port Micro USB qui s’appelle **Debug USB**. Chaque composant est mis en évidence dans l’image suivante :

    ![Localiser les composants clés sur le tableau du kit d’évaluation du Microchip E54](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. Branchez le câble Micro USB au port **Debug USB** du Microchip E54, puis branchez-le à votre ordinateur.

    > [!NOTE]
    > Pour plus d’informations sur la configuration et la prise en main du Microchip E54, consultez le [Guide de l’utilisateur sur SAM E54 Xplained Pro](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf) si vous le souhaitez.

1. Utilisez le câble Ethernet pour connecter le Microchip E54 à un port Ethernet.

### <a name="configure-termite"></a>Configurer Termite

Vous allez utiliser l’application **Termite** pour superviser la communication et confirmer que votre appareil est correctement configuré. Dans cette section, vous allez configurer **Termite** pour superviser le port série de votre appareil.

1. Démarrez **Termite**.

1. Sélectionnez **Paramètres**.

1. Dans la boîte de dialogue **Paramètres du port série**, vérifiez les paramètres suivants et mettez à jour si nécessaire :
    * **Vitesse (en bauds)**  : 115 200
    * **Port** : port auquel votre Microchip E54 est connecté. Si plusieurs options de port sont disponibles dans la liste déroulante, vous pouvez trouver le port approprié à utiliser. Ouvrez le **Gestionnaire d'appareils** Windows et affichez les **Ports** pour identifier le port à utiliser.
    * **Contrôle de flux** : DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Capture d’écran des paramètres du port série dans l’application Termite":::

1. Sélectionnez **OK**.

Termite est maintenant prêt à recevoir la sortie du Microchip E54.

### <a name="configure-build-flash-and-run-the-image"></a>Configurer, générer, flasher et exécuter l’image

1. Ouvrez l’application **IAR EW pour ARM** sur votre ordinateur.

1. Sélectionnez **File > Open workspace**, accédez au dossier **same54Xpro\iar** à partir du dossier de travail où vous avez extrait le fichier zip, puis ouvrez l’espace de travail EWARM **_azure_rtos.eww_**.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/open-project-iar.png" alt-text="Ouverture de l’espace de travail IAR":::

1. Cliquez avec le bouton droit sur le projet **sample_azure_iot_embedded_sdk_pnp** dans le volet **Workspace** de gauche, puis sélectionnez **Set as active**.

1. Développez l’exemple, puis développez le dossier **Sample** et ouvrez le fichier sample_config.h.

1. Supprimez les marques de commentaire de la directive `#define ENABLE_DPS_SAMPLE` au début du fichier.

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Définissez les constantes d’informations de l’appareil Azure IoT sur les valeurs que vous avez enregistrées après avoir créé les ressources Azure. La constante `ENDPOINT` est définie sur le point de terminaison global du service DPS (Azure Device Provisioning Service).

    |Nom de la constante|Valeur|
    |-------------|-----|
    | `ENDPOINT` | "global.azure-devices-provisioning.net" |
    | `ID_SCOPE` | {*Valeur d’étendue de votre ID*} |
    | `REGISTRATION_ID` | {*Valeur d’ID de votre appareil*} |
    | `DEVICE_SYMMETRIC_KEY` | {*Valeur de votre clé primaire*} |

    > [!NOTE]
    > Les valeurs `ENDPOINT`, `ID_SCOPE` et `REGISTRATION_ID` sont définies dans une instruction `#ifndef ENABLE_DPS_SAMPLE`. Veillez à définir les valeurs dans l’instruction `#else`, qui sera utilisée lorsque la valeur `ENABLE_DPS_SAMPLE` sera définie.

1. Enregistrez le fichier .

1. Sélectionnez **Project > Batch Build**. Ensuite, sélectionnez **build_all** et **Make** pour générer tous les projets. Vous verrez la sortie de build dans le volet **Build**. Confirmez la réussite de la compilation et la liaison de tous les exemples de projets.

1. Sélectionnez le bouton vert **Download and Debug** dans la barre d’outils pour télécharger le programme.

1. Une fois le téléchargement de l’image terminé, sélectionnez **Go** pour exécuter l’exemple.

### <a name="confirm-device-connection-details"></a>Vérifier les détails de connexion de l’appareil

Dans l’application **Termite**, confirmez les valeurs de point de contrôle suivantes pour vérifier que l’appareil est initialisé et connecté à Azure IoT.

```output
DHCP In Progress...
IP address: 192.168.0.22
Mask: 255.255.255.0
Gateway: 192.168.0.1
DNS Server address: 75.75.75.75
SNTP Time Sync...
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-597ffb0b-3dbe-4784-ba3c-fdefd120a44a.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Telemetry message send: {"temperature":22}.
Receive twin properties: {"desired":{"$version":1},"reported":{"maxTempSinceLastReboot":22,"$version":8}}
Failed to parse value
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

Laissez Termite ouvert pour surveiller la sortie de l’appareil dans les étapes suivantes.

:::zone-end
:::zone pivot="iot-toolset-mplab"

## <a name="prerequisites"></a>Prérequis

* PC exécutant Microsoft Windows 10

* Matériel

  * [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * USB 2.0, câble mâle-mâle micro USB
  * Accès Ethernet câblé
  * Câble Ethernet

* [Termite](https://www.compuphase.com/software_termite.htm). Dans la page web, sous **Downloads and license**, choisissez l’installation complète. Termite est un terminal RS232 que vous utiliserez pour superviser la sortie de votre appareil.

* [MPLAB X IDE 5.35](https://www.microchip.com/mplab/mplab-x-ide).

* [Compilateur MPLAB XC32/32++ version 2.4.0 ou ultérieure](https://www.microchip.com/mplab/compilers).

* Téléchargez le fichier [Azure_RTOS_6.1_ATSAME54-XPRO_MPLab_Samples_2020_10_10.zip](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_MPLab_Samples_2020_10_10.zip) et décompressez-le dans un répertoire de travail. Choisissez un répertoire avec un chemin bref pour éviter les erreurs du compilateur lors de la génération.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour connecter le Microchip E54 à Azure, vous allez connecter le Microchip E54 à voter ordinateur, modifier un fichier de configuration pour les paramètres Azure IoT, générer l’image et la flasher sur l’appareil.

### <a name="connect-the-device"></a>Connecter l’appareil

1. Sur le Microchip E54, localisez le bouton **Reset**, le port **Ethernet** et le port Micro USB qui s’appelle **Debug USB**. Chaque composant est mis en évidence dans l’image suivante :

    ![Localiser les composants clés sur le tableau du kit d’évaluation du Microchip E54](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. Branchez le câble Micro USB au port **Debug USB** du Microchip E54, puis branchez-le à votre ordinateur.

    > [!NOTE]
    > Pour plus d’informations sur la configuration et la prise en main du Microchip E54, consultez le [Guide de l’utilisateur sur SAM E54 Xplained Pro](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf) si vous le souhaitez.

1. Utilisez le câble Ethernet pour connecter le Microchip E54 à un port Ethernet.

### <a name="configure-termite"></a>Configurer Termite

Vous allez utiliser l’application **Termite** pour superviser la communication et confirmer que votre appareil est correctement configuré. Dans cette section, vous allez configurer **Termite** pour superviser le port série de votre appareil.

1. Démarrez **Termite**.

1. Sélectionnez **Paramètres**.

1. Dans la boîte de dialogue **Paramètres du port série**, vérifiez les paramètres suivants et mettez à jour si nécessaire :
    * **Vitesse (en bauds)**  : 115 200
    * **Port** : port auquel votre Microchip E54 est connecté. Si plusieurs options de port sont disponibles dans la liste déroulante, vous pouvez trouver le port approprié à utiliser. Ouvrez le **Gestionnaire d'appareils** Windows et affichez les **Ports** pour identifier le port à utiliser.
    * **Contrôle de flux** : DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Capture d’écran des paramètres du port série dans l’application Termite":::

1. Sélectionnez **OK**.

Termite est maintenant prêt à recevoir la sortie du Microchip E54.

### <a name="configure-build-flash-and-run-the-image"></a>Configurer, générer, flasher et exécuter l’image

1. Ouvrez **MPLAB X IDE** sur votre ordinateur.

1. Sélectionnez **File > Open project**. Dans la boîte de dialogue d’ouverture de projet, accédez au dossier **same54Xpro\mplab** à partir du dossier de travail où vous avez extrait le fichier zip. Sélectionnez tous les projets (ne sélectionnez pas les dossiers **common_hardware_code** ou **docs**), puis sélectionnez **Open Project**.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/open-project-mplab.png" alt-text="Ouverture de projets dans l’IDE MPLab":::

1. Cliquez avec le bouton droit sur le projet **sample_azure_iot_embedded_sdk_pnp** dans le volet **Projects** de gauche, puis sélectionnez **Set as Main Project**.

1. Développez le projet **sample_azure_iot_embedded_sdk_pnp**, puis développez le dossier **Header Files** et ouvrez le fichier sample_config.h.

1. Supprimez les marques de commentaire de la directive `#define ENABLE_DPS_SAMPLE` au début du fichier.

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Définissez les constantes d’informations de l’appareil Azure IoT sur les valeurs que vous avez enregistrées après avoir créé les ressources Azure. La constante `ENDPOINT` est définie sur le point de terminaison global du service DPS (Azure Device Provisioning Service).

    |Nom de la constante|Valeur|
    |-------------|-----|
    | `ENDPOINT` | "global.azure-devices-provisioning.net" |
    | `ID_SCOPE` | {*Valeur d’étendue de votre ID*} |
    | `REGISTRATION_ID` | {*Valeur d’ID de votre appareil*} |
    | `DEVICE_SYMMETRIC_KEY` | {*Valeur de votre clé primaire*} |

    > [!NOTE]
    > Les valeurs `ENDPOINT`, `ID_SCOPE` et `REGISTRATION_ID` sont définies dans une instruction `#ifndef ENABLE_DPS_SAMPLE`. Veillez à définir les valeurs dans l’instruction `#else`, qui sera utilisée lorsque la valeur `ENABLE_DPS_SAMPLE` sera définie.

1. Enregistrez le fichier .

1. Avant de pouvoir générer l’exemple, vous devez générer les bibliothèques dépendantes du projet **sample_azure_iot_embedded_pnp** : **threadx**, **netxduo** et **same54_lib**. Pour générer chaque bibliothèque, cliquez avec le bouton droit sur son projet dans le volet **Projects**, puis sélectionnez **Build**. Attendez que chaque build se termine avant de passer à la bibliothèque suivante.

1. Une fois que toutes les bibliothèques prérequises ont été correctement générées, cliquez avec le bouton droit sur le projet **sample_azure_iot_embedded_pnp**, puis sélectionnez **Build**.

1. Sélectionnez **Debug > Debug Main Project** dans le menu du haut pour télécharger et démarrer le programme.

1. Si une boîte de dialogue **Tool not Found** s’affiche, sélectionnez **connect SAM E54 board**, puis **OK**.

1. Le téléchargement et l’exécution du programme peuvent prendre quelques minutes. Une fois que le programme a été téléchargé avec succès et qu’il est en cours d’exécution, l’état suivant s’affiche dans le volet **Output** de MPLAB X IDE.

    ```output
    Programming complete
    
    Running
    ```

### <a name="confirm-device-connection-details"></a>Vérifier les détails de connexion de l’appareil

Dans l’application **Termite**, confirmez les valeurs de point de contrôle suivantes pour vérifier que l’appareil est initialisé et connecté à Azure IoT.

```output
DHCP In Progress...
IP address: 192.168.0.22
Mask: 255.255.255.0
Gateway: 192.168.0.1
DNS Server address: 75.75.75.75
SNTP Time Sync...
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-597ffb0b-3dbe-4784-ba3c-fdefd120a44a.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Telemetry message send: {"temperature":22}.
Receive twin properties: {"desired":{"$version":1},"reported":{"maxTempSinceLastReboot":22,"$version":8}}
Failed to parse value
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

Laissez Termite ouvert pour surveiller la sortie de l’appareil dans les étapes suivantes.

:::zone-end

## <a name="verify-the-device-status"></a>Vérifier l’état de l’appareil

Pour consulter l’état de l’appareil dans le portail IoT Central :

:::zone pivot="iot-toolset-cmake"
1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Vérifiez que l’**état de l'appareil** est mis à jour en *approvisionné*.
1. Vérifiez que le **modèle d’appareil** est mis à jour en *Guide de prise en main*.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status.png" alt-text="Capture d’écran de l’état de l’appareil dans IoT Central":::
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Vérifiez que l’**état de l'appareil** est mis à jour en *approvisionné*.
1. Vérifiez que le **modèle d’appareil** est mis à jour en *Thermostat*.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status-iar.png" alt-text="Capture d’écran de l’état de l’appareil dans IoT Central":::
:::zone-end

## <a name="view-telemetry"></a>Afficher les données de télémétrie

Avec IoT Central, vous pouvez afficher le flux de télémétrie de votre appareil dans le cloud.

Pour afficher la télémétrie dans le portail IoT Central :

1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Sélectionnez l’appareil dans la liste.
1. Affichez la télémétrie lorsque l’appareil envoie des messages au cloud sous l’onglet **Vue d’ensemble**.

    :::zone pivot="iot-toolset-cmake"
    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry.png" alt-text="Capture d’écran de la télémétrie de l’appareil dans IoT Central":::
    :::zone-end
    :::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry-iar.png" alt-text="Capture d’écran de la télémétrie de l’appareil dans IoT Central":::
    :::zone-end

    > [!NOTE]
    > Vous pouvez également surveiller la télémétrie à partir de l’appareil à l’aide de l’application Termite.

## <a name="call-a-direct-method-on-the-device"></a>Appeler une méthode directe sur l’appareil

Vous pouvez également utiliser IoT Central pour appeler une méthode directe que vous avez implémentée sur votre appareil. Les méthodes directes portent un nom et peuvent éventuellement avoir une charge utile JSON, une connexion configurable et un délai d’expiration de méthode. Dans cette section, vous appelez une méthode qui vous permet d’activer ou de désactiver un voyant.

Pour appeler une méthode dans le portail IoT Central :
:::zone pivot="iot-toolset-cmake"

1. Sélectionnez l’onglet **Commande** sur la page de l’appareil.

1. Dans la liste déroulante **État**, sélectionnez **Vrai**, puis **Exécuter**.  Le voyant doit s’allumer.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method.png" alt-text="Capture d’écran de l’appel d’une méthode directe sur un appareil dans IoT Central":::

1. Dans la liste déroulante **État**, sélectionnez **Faux**, puis **Exécuter**. Le voyant doit s’éteindre.
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"

1. Sélectionnez l’onglet **Commande** sur la page de l’appareil.

1. Dans le champ **Depuis**, utilisez les sélecteurs de date et d’heure pour définir une heure, puis sélectionnez **Exécuter**.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method-iar.png" alt-text="Capture d’écran de l’appel d’une méthode directe sur un appareil dans IoT Central":::

1. Vous pouvez voir l’appel de commande dans Termite :

    ```output
    Receive method call: getMaxMinReport, with payload:"2021-10-14T17:45:00.000Z"
    ```

    > [!NOTE]
    > Vous pouvez également voir l’appel de commande et la réponse sous l’onglet **Données brutes** de la page de l’appareil dans IoT Central.
:::zone-end

## <a name="view-device-information"></a>Affichage des informations sur l’appareil

Vous pouvez afficher les informations de l’appareil à partir d’IoT Central.

Sélectionnez l’onglet **À propos de** sur la page de l’appareil.

:::zone pivot="iot-toolset-cmake"
:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about.png" alt-text="Capture d’écran des informations sur l’appareil dans IoT Central":::
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about-iar.png" alt-text="Capture d’écran des informations sur l’appareil dans IoT Central":::
:::zone-end

## <a name="troubleshoot-and-debug"></a>Résoudre les problèmes et déboguer

Si vous rencontrez des problèmes pour générer le code d’appareil, flasher l’appareil ou le connecter, consultez [Résolution des problèmes](troubleshoot-embedded-device-quickstarts.md).

:::zone pivot="iot-toolset-cmake"
Pour déboguer l’application, consultez [Débogage avec Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"
Pour obtenir de l’aide sur le débogage de l’application, consultez les sélections sous **Help** dans **IAR EW pour ARM**.  
:::zone-end
:::zone pivot="iot-toolset-mplab"
Pour obtenir de l’aide sur le débogage de l’application, consultez les sélections sous **Help** dans **MPLAB X IDE**.  
:::zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources Azure créées dans le cadre de ce démarrage rapide, vous pouvez les supprimer du portail IoT Central.

Pour supprimer la totalité de l’exemple d’application Azure IoT Central et tous ses appareils et ressources :

1. Sélectionnez **Administration** > **Votre application**.
1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une image personnalisée contenant un exemple de code Azure RTOS, puis flashé l’image sur l’appareil Microchip E54. Vous avez également utilisé le portail IoT Central pour créer des ressources Azure, connecter le Microchip E54 de façon sécurisée à Azure, afficher la télémétrie et envoyer des messages.

L’étape suivante consiste à explorer les articles suivants pour en savoir plus sur l’utilisation des kits IoT device SDK pour connecter des appareils à Azure IoT. 

> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Hub](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS fournit aux fabricants OEM les composants permettant de sécuriser la communication et de créer du code et l’isolation des données à l’aide des mécanismes sous-jacents de protection matérielle MCU/MPU. Toutefois, chaque fabricant OEM est tenu de garantir que son appareil répond aux exigences de sécurité en constante évolution.
