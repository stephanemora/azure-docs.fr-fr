---
title: Gérer en bloc des appareils connectés à la supervision à distance - Azure | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment gérer en bloc les appareils connectés à une solution de supervision à distance.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: 57d2ac4f1e80c24a750e0e5f8eb22850718594a1
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071028"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Tutoriel : Gérer vos appareils connectés en bloc

Dans ce tutoriel, vous utilisez l’accélérateur de solution de supervision à distance pour gérer en bloc la configuration de vos appareils connectés.

En tant qu’opérateur chez Contoso, vous devez configurer un groupe d’appareils avec une nouvelle version de microprogramme. Vous ne voulez pas avoir à mettre à jour le microprogramme individuellement sur chaque appareil. Pour mettre à jour le microprogramme sur un groupe d’appareils, vous pouvez utiliser des groupes d’appareils et la gestion automatique des appareils dans l’accélérateur de solution de supervision à distance. Dès qu’un appareil que vous ajoutez au groupe d’appareils est mis en ligne, il obtient le dernier microprogramme.

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Créer un groupe d’appareils
> * Préparer et héberger le microprogramme
> * Créer une configuration d’appareil dans le portail Azure
> * Importer une configuration d’appareil dans votre solution de supervision à distance
> * Déployer la configuration sur les appareils du groupe d’appareils
> * Surveiller le déploiement

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’une instance déployée de l’accélérateur de solution de supervision à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé l’accélérateur de solution de supervision à distance, vous devez suivre le guide de démarrage rapide [Déployer une solution de supervision à distance cloud](quickstart-remote-monitoring-deploy.md).

Vous avez besoin d’un compte de stockage Azure pour héberger vos fichiers de microprogramme. Vous pouvez utiliser un compte de stockage existant ou [créer un compte de stockage](../storage/common/storage-account-create.md) dans votre abonnement.

Le tutoriel utilise un [DevKit IoT](https://microsoft.github.io/azure-iot-developer-kit/) comme exemple d’appareil.

Les logiciels suivants doivent être installés sur votre machine locale :

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
* L’extension VS Code [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench).

Avant de commencer :

* Vérifiez que la [version du chargeur de démarrage sur votre appareil DevKit IoT est 1.4.0 ou ultérieure](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Vérifiez que le SDK IoT DevKit est à la même version que le chargeur de démarrage. Vous pouvez mettre à jour le SDK IoT DevKit à l’aide d’Azure IoT Workbench dans VS Code. Ouvrez la palette de commandes, puis entrez **Arduino : Gestionnaire de cartes**. Pour plus d’informations, consultez [Préparer l’environnement de développement](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Vous devez également connecter au moins un appareil DevKit IoT à votre accélérateur de solution de supervision à distance. Si aucun appareil DevKit IoT n’est connecté, consultez [Connecter MXChip IoT DevKit AZ3166 à l’accélérateur de solution de supervision à distance IoT](./iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md).

## <a name="navigate-to-the-dashboard"></a>Accédez au tableau de bord.

Pour afficher le tableau de bord de solution de supervision à distance dans votre navigateur, accédez d’abord à [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard). Vous pouvez être invité à vous connecter les informations d’identification de votre abonnement Azure.

Puis cliquez sur **Lancer** sur la vignette de l’accélérateur de solution de supervision à distance que vous avez déployé dans le [Démarrage rapide](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Créer un groupe d’appareils

Pour mettre automatiquement à jour le microprogramme sur un groupe d’appareils, les appareils doivent être membres d’un groupe d’appareils dans votre solution de supervision à distance :

1. Sur la page **Appareils**, sélectionnez tous les appareils **DevKit IoT** que vous avez connectés à l’accélérateur de solution. Cliquez ensuite sur **Tâches**.

1. Dans le volet **Tâches**, sélectionnez **Balises**, définissez **AddDevKitTag** comme nom de tâche, puis ajoutez une balise de texte nommée **IsDevKitDevice** et ayant la valeur **Y**. Cliquez ensuite sur **Appliquer**.

1. Vous pouvez maintenant utiliser les valeurs des balises pour créer un groupe d’appareils. Dans la page **Appareils**, cliquez sur **Gérer les groupes d’appareils**.

1. Créez un filtre de texte qui utilise le nom de balise **IsDevKitDevice** et la valeur **Y** dans la condition. Enregistrez le groupe d’appareils sous la forme **Appareils DevKit IoT**.

Plus loin dans ce tutoriel, vous utilisez ce groupe d’appareils pour appliquer une configuration d’appareil qui met à jour le microprogramme de tous les membres.

## <a name="prepare-and-host-the-firmware"></a>Préparer et héberger le microprogramme

L’extension VS Code [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) inclut l’exemple de code d’appareil pour la mise à jour du microprogramme.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Ouvrez l’exemple Firmware OTA (Microprogramme OTA) dans VS Code

1. Vérifiez que votre DevKit IoT n’est pas connecté à votre ordinateur. Démarrez VS Code, puis connectez le DevKit sur votre ordinateur.

1. Appuyez sur **F1** pour ouvrir la palette de commandes, puis tapez et sélectionnez **IoT Workbench : Exemples**. Sélectionnez ensuite **DevKit IoT** comme carte.

1. Recherchez **Microprogramme OTA** et cliquez sur **Ouvrir l’exemple**. Une nouvelle fenêtre VS Code s’ouvre et affiche le dossier de projet **firmware_ota** :

    ![Dans IoT Workbench, sélectionner l’exemple OTA Firmware (Microprogramme OTA)](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Générer le nouveau microprogramme

La version initiale du microprogramme de l’appareil est 1.0.0. Le nouveau microprogramme doit avoir un numéro de version ultérieure.

1. Dans VS Code, ouvrez le fichier **FirmwareOTA.ino**, et remplacez la valeur de `currentFirmwareVersion``1.0.0` par `1.0.1` :

    ![Changer la version du microprogramme](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Ouvrez la palette de commandes, puis tapez et sélectionnez **IoT Workbench : Appareil**. Sélectionnez ensuite **Device Compile** (Compilation de l’appareil) pour compiler le code :

    ![Compilation de l’appareil](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code enregistre le fichier compilé dans le dossier `.build` du projet. En fonction de vos paramètres, VS Code peut masquer le dossier `.build` en mode Explorateur.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Générer la valeur CRC et calculer la taille de fichier du microprogramme

1. Ouvrez la palette de commandes, puis tapez et sélectionnez **IoT Workbench : Appareil**. Sélectionnez ensuite **Generate CRC** (Générer CRC) :

    ![Générer CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code génère et imprime la valeur CRC, le nom de fichier et le chemin du microprogramme ainsi que la taille du fichier dans la fenêtre Sortie. Notez ces valeurs pour une utilisation ultérieure :

    ![Informations CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Charger le microprogramme dans le cloud

Utilisez votre compte de stockage Azure pour héberger votre nouveau fichier de microprogramme dans le cloud.

1. Accédez à votre compte de stockage dans le portail Azure. Dans la section Services, cliquez sur **Objets blob**. Créez un conteneur public appelé **firmware** (microprogramme) pour stocker vos fichiers de microprogramme :

    ![Créer un dossier](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Pour charger votre fichier de microprogramme dans le conteneur, sélectionnez le conteneur **firmware**, puis cliquez sur **Charger**.

1. Sélectionnez le fichier **FirmwareOTA.ino.bin**. Vous avez noté le chemin complet de ce fichier dans la section précédente.

1. Une fois le chargement du fichier de microprogramme terminé, notez l’URL du fichier.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Générer et charger le microprogramme d’origine sur l’appareil DevKit IoT

1. Dans VS Code, ouvrez le fichier **FirmwareOTA.ino**, et rétablissez la valeur de `currentFirmwareVersion``1.0.0` :

    ![Version 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Ouvrez la palette de commandes, puis tapez et sélectionnez **IoT Workbench : Appareil**. Sélectionnez ensuite **Device Upload** (Chargement d’appareil) :

    ![Chargement d’appareil](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code vérifie et charge le code sur votre appareil DevKit IoT.

1. Une fois le chargement terminé, l’appareil DevKit IoT redémarre. Une fois le redémarrage effectué, l’écran d’IoT DevKit affiche **FW version : 1.0.0**, et indique qu’il recherche un nouveau microprogramme :

    ![La photographie montre l’appareil I o T DevKit avec son écran affichant la version du microprogramme et recherchant le nouveau microprogramme.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Créer une configuration d’appareil

Une configuration d’appareil spécifie l’état souhaité de vos appareils. En général, un développeur [crée la configuration](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) sur la page **Configuration de l’appareil IoT** dans le portail Azure. Une configuration d’appareil est un document JSON qui spécifie l’état souhaité de vos appareils et un ensemble de métriques.

Enregistrez la configuration suivante sous la forme d’un fichier appelé **firmware-update.json** sur votre ordinateur local. Remplacez les espaces réservés `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` et `YOURPACKAGESIZE` par les valeurs que vous avez notées précédemment :

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Vous utilisez ce fichier de configuration dans la prochaine section.

## <a name="import-a-configuration"></a>Importer une configuration

Dans cette section, vous importez la configuration d’appareil sous la forme d’un package dans l’accélérateur de solution de supervision à distance. En général, un opérateur effectue cette tâche.

1. Dans l’interface utilisateur web de la supervision à distance, accédez à la page **Packages**, puis cliquez sur **+Nouveau package** :

    ![Nouveau package](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Dans le volet **Nouveau package**, choisissez **Configuration de l’appareil** comme type de package et **Microprogramme** comme type de configuration. Cliquez sur **Parcourir** pour rechercher le fichier **firmware-update.json** sur votre ordinateur local, puis cliquez sur **Charger** :

    ![Charger le package](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. La liste de packages inclut désormais le package **firmware-update**.

## <a name="deploy-the-configuration-to-your-devices"></a>Déployer la configuration sur vos appareils

Dans cette section, vous créez et exécutez un déploiement qui applique la configuration de l’appareil à vos appareils DevKit IoT.

1. Dans l’interface utilisateur web de la supervision à distance, accédez à la page **Déploiements**, puis cliquez sur **+Nouveau déploiement** :

    ![Nouveau déploiement](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Dans le panneau **Nouveau déploiement**, créez un déploiement avec les paramètres suivants :

    |Option|Valeur|
    |---|---|
    |Nom|Déployer la mise à jour du microprogramme|
    |Type de package|Configuration de l’appareil|
    |Type de configuration|Microprogramme|
    |Package|firmware-update.json|
    |Groupe d’appareils|Appareils DevKit IoT|
    |Priority|10|

    ![Créer un déploiement](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Cliquez sur **Appliquer**. Vous voyez un nouveau déploiement dans la page **Déploiements** qui affiche les métriques suivantes :

    * **Ciblé** indique le nombre d’appareils qui se trouvent dans le groupe d’appareils.
    * **Appliqué** indique le nombre d’appareils qui ont été mis à jour avec le contenu de la configuration.
    * **Réussites** indique le nombre d’appareils du déploiement qui signalent une réussite.
    * **Échecs** indique le nombre d’appareils du déploiement qui signalent un échec.

## <a name="monitor-the-deployment"></a>Surveiller le déploiement

Après quelques minutes, le DevKit IoT récupère les informations du nouveau microprogramme et commence à le télécharger sur l’appareil :

![La photographie montre l’appareil I o T DevKit avec l’écran affichant la nouvelle version de microprogramme téléchargée.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

En fonction de la vitesse de votre réseau, le téléchargement peut prendre quelques minutes. Une fois le microprogramme téléchargé, l’appareil vérifie la taille du fichier et la valeur CRC. L’écran sur le MXChip affiche **passed** (réussi) si la vérification aboutit.

![La photographie montre l’appareil I o T DevKit avec son écran affichant la version du microprogramme et transmis pour vérification réussie.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Si la vérification aboutit, l’appareil redémarre. Vous voyez un compte à rebours de **5** à **0** avant que le redémarrage ne se produise.

![La photographie montre l’appareil I o T DevKit se préparant à redémarrer.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Après le redémarrage, le chargeur de démarrage DevKit IoT met à niveau le microprogramme vers la nouvelle version. La mise à jour peut durer plusieurs secondes. Pendant cette phase, la LED RVB dans l’appareil est rouge et l’écran est vide.

![La photographie montre l’appareil I o T DevKit effectuant la mise à niveau. Il n’y a pas d’affichage, mais y a un R G B L E D lumineux rouge.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Une fois le redémarrage terminé, la version 1.0.1 du microprogramme est en cours d’exécution sur votre appareil DevKit IoT.

![La photographie montre l’appareil I o T DevKit avec l’écran affichant la nouvelle version de microprogramme.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Sur la page **Déploiements**, cliquez sur un déploiement pour connaître l’état de vos appareils quand ils se mettent à jour. Vous pouvez voir l’état de chaque appareil figurant dans votre groupe d’appareils ainsi que les métriques personnalisées que vous avez définies.

![Détails du déploiement](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment mettre à jour le microprogramme d’un groupe d’appareils connectés à votre solution. Pour mettre à jour les appareils, votre solution utilise la gestion automatique des appareils. Pour en savoir plus sur la fonctionnalité de gestion automatique des appareils dans le hub IoT sous-jacent de votre solution, consultez [Configurer et superviser des appareils IoT à grande échelle à l’aide du portail Azure](../iot-hub/iot-hub-automatic-device-management.md).