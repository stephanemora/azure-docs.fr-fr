---
title: Connecter un appareil SensorTile.box à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil SensorTile.box à votre application Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: ce0c5abe6e89094623c07afa2d1c85903e0e7ee7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877444"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Connecter un appareil SensorTile.box à votre application Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil SensorTile.box à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des ressources suivantes :

* Un appareil SensorTile.box. Pour plus d’informations, consultez [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* L’application ST BLE Sensor installée sur votre appareil Android. Vous pouvez [la télécharger ici](https://play.google.com/store/apps/details?id=com.st.bluems). Pour plus d’informations, consultez : [ST BLE Sensor](https://www.st.com/stblesensor)
* Une application Azure IoT Central créée à partir du modèle d’application **Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Pour ajouter le modèle d’appareil **SensorTile.box** dans votre application IoT Central, rendez-vous sur la page **Modèles d’appareil**, cliquez sur **+ Nouveau** et sélectionnez le modèle **SensorTile.box**.

### <a name="get-your-device-connection-details"></a>Obtenir des détails de la connexion de votre appareil

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **SensorTile.box** et prenez note des détails de connexion de l’appareil : **ID de portée**, **ID d’appareil** et **Clé primaire** :

1. Ajoutez un appareil à partir de l’Explorateur d’appareils. Sélectionnez **+ Nouveau > Réel** pour ajouter un appareil réel.

    * Entrez un **ID d’appareil** en minuscules ou utilisez **l’ID d’appareil** suggéré.
    * Entrez un **Nom de l’appareil** ou utilisez le nom suggéré.

    ![Ajout d’un appareil](media/howto-connect-sensortile/real-device.png)

1. Pour obtenir des informations détaillées sur la connexion de l’appareil, telles que les paramètres **ID de portée**, **ID d’appareil** et **Clé primaire**, sélectionnez **Connecter** sur la page de l’appareil.

    ![Informations de connexion](media/howto-connect-sensortile/connect-device.png)

1. Prenez note des détails de la connexion. Vous êtes temporairement déconnecté d’Internet lorsque vous préparez votre appareil DevKit à l’étape suivante.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurer l’appareil SensorTile.box avec l’application mobile

Dans cette section, vous allez apprendre à envoyer (push) le microprogramme de l’application sur l’appareil. Vous allez ensuite découvrir comment envoyer les données de l’appareil à IoT Central via l’application mobile ST BLE Sensor à l’aide de la connectivité Bluetooth Low Energy (BLE).

1. Ouvrez l’application ST BLE Sensor, puis appuyez sur le bouton **Créer une application**.

    ![Créer une application](media/howto-connect-sensortile/create-app.png)

1. Sélectionnez l’application **Baromètre**.
1. Appuyez sur le bouton de chargement.

    ![Chargement de l’application Baromètre](media/howto-connect-sensortile/barometer-upload.png)

1. Appuyez sur le bouton de lecture associé à votre appareil SensorTile.box.
1. Lorsque le processus est terminé, l’appareil SensorTile.box diffuse en continu la température, la pression et l’humidité sur BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Connecter l’appareil SensorTile.box au cloud

Dans cette section, vous allez apprendre à connecter l’appareil SensorTile.box à l’application mobile et l’application mobile au cloud.

1. À l’aide du menu de gauche, sélectionnez le bouton **Cloud Logging** (Journalisation du cloud).

    ![Journalisation du cloud](media/howto-connect-sensortile/cloud-logging.png)

1. Sélectionnez **Azure IoT Central** comme fournisseur de services cloud.
1. Insérez l’ID d’appareil et l’ID de portée qui ont été notés précédemment.

    ![Informations d'identification](media/howto-connect-sensortile/credentials.png)

1. Sélectionnez la case d’option **Clé de l’application**.
1. Cliquez sur **Connecter** et sélectionnez les données de télémétrie à charger.
1. Après quelques secondes, les données s’affichent sur le tableau de bord de l’application IoT Central.

## <a name="sensortilebox-device-template-details"></a>Détails du modèle d’appareil SensorTile.box

Application créée à partir du modèle d’appareil SensorTile.box avec les caractéristiques suivantes :

### <a name="telemetry"></a>Télémétrie

| Nom du champ     | Units  | Minimale | Maximale | Nombre de décimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humidité       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pression       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1 000   | 1 000    | 0              |
| magnetometerY  | mgauss | -1 000   | 1 000    | 0              |
| magnetometerZ  | mgauss | -1 000   | 1 000    | 0              |
| accelerometerX | mg     | -2 000   | 2000    | 0              |
| accelerometerY | mg     | -2 000   | 2000    | 0              |
| accelerometerZ | mg     | -2 000   | 2000    | 0              |
| gyroscopeX     | dps   | -3 276   | 3 276    | 1              |
| gyroscopeY     | dps   | -3 276   | 3 276    | 1              |
| gyroscopeZ     | dps   | -3 276   | 3 276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un appareil SensorTile.box à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [configurer un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
