---
title: Connecter un appareil SensorTile.box à votre application Azure IoT Central | Microsoft Docs
description: En tant que périphérique développeur, découvrez comment connecter un appareil SensorTile.box à votre application Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472184"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Connecter SensorTile.box appareil à votre application Azure IoT Central

Cet article décrit comment, en tant que périphérique développeur, pour connecter un appareil de SensorTile.box à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes décrites dans cet article, vous devez les ressources suivantes :

* Un appareil SensorTile.box. Pour plus d’informations, consultez [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* L’application de ST BLE Sensor installée sur votre appareil Android, vous pouvez [le télécharger à partir d’ici](https://play.google.com/store/apps/details?id=com.st.bluems). Pour plus d’informations, visitez : [Capteur de BLE ST](https://www.st.com/stblesensor)
* Une application Azure IoT Central créée à partir de la **DevKits** ce modèle d’application. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Ajouter le **SensorTile.box** modèle d’appareil dans votre application IoT Central en vous rendant sur le **modèles de périphériques** page, en cliquant sur **+ nouveau**et en sélectionnant le **SensorTile.box** modèle.

### <a name="get-your-device-connection-details"></a>Obtenir des détails de la connexion de votre appareil

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir de la **SensorTile.box** modèle d’appareil et notez les détails de connexion d’appareil : **ID de portée**, **ID d’appareil**, et **clé primaire**:

1. Ajouter un appareil à partir de Device Explorer. Sélectionnez **+ Nouveau > réel** pour ajouter un appareil réel.

    * Entrez un caractère minuscule **ID d’appareil**, ou utilisez le texte suggéré **ID d’appareil**.
    * Entrez un **nom de l’appareil**, ou utilisez le nom suggéré

    ![Ajouter un appareil](media/howto-connect-sensortile/real-device.png)

1. Pour obtenir des informations de connexion, de l’appareil **ID de portée**, **ID d’appareil**, et **clé primaire**, sélectionnez **Connect** sur la page de l’appareil.

    ![Détails de la connexion](media/howto-connect-sensortile/connect-device.png)

1. Prenez note des détails de connexion. Vous êtes temporairement déconnecté d’internet lorsque vous préparez votre appareil DevKit à l’étape suivante.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurer le SensorTile.box avec l’application mobile

Dans cette section, vous allez apprendre à transmettre le microprogramme de l’application sur l’appareil. Vous puis comment envoyer les données d’appareil à IoT Central via l’application mobile ST BLE Sensor à l’aide de la connectivité de Bluetooth Low Energy (BLE).

1. Ouvrez l’application de ST BLE Sensor, puis appuyez sur la **créer une application** bouton.

    ![Créer une application](media/howto-connect-sensortile/create-app.png)

1. Sélectionnez le **baromètre** application.
1. Appuyez sur le bouton Charger.

    ![Téléchargement de baromètre](media/howto-connect-sensortile/barometer-upload.png)

1. Appuyez sur le bouton de lecture associé à votre SensorTile.box.
1. Lorsque le processus est terminé, le SensorTile.box diffuse en continu la température, pression et humidité sur BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Connectez le SensorTile.box au cloud

Dans cette section, vous allez apprendre à connecter le SensorTile.box à l’application mobile et l’application mobile vers le cloud.

1. À l’aide du menu de gauche, sélectionnez le **Cloud journalisation** bouton.

    ![Journalisation de cloud](media/howto-connect-sensortile/cloud-logging.png)

1. Sélectionnez **Azure IoT Central** comme fournisseur de cloud.
1. Insérez l’ID de périphérique et l’ID de portée qui ont été mentionnées précédemment.

    ![Informations d'identification](media/howto-connect-sensortile/credentials.png)

1. Sélectionnez le **clé d’Application** case d’option.
1. Cliquez sur **Connect** et sélectionnez les données de télémétrie que vous souhaitez télécharger.
1. Après quelques secondes, les données apparaissent sur le tableau de bord application IoT Central.

## <a name="sensortilebox-device-template-details"></a>Détails du modèle SensorTile.box appareil

Une application créée à partir du modèle d’appareil SensorTile.box avec les caractéristiques suivantes :

### <a name="telemetry"></a>Télémétrie

| Nom du champ     | Unités  | Minimale | Maximale | Nombre de décimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humidité       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pression       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1 000    | 0              |
| magnetometerY  | mgauss | -1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1 000    | 0              |
| accelerometerX | mg     | -2 000   | 2000    | 0              |
| accelerometerY | mg     | -2 000   | 2000    | 0              |
| accelerometerZ | mg     | -2 000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un SensorTile.box à votre application Azure IoT Central, l’étape suivante suggérée consiste à en savoir plus [comment définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
