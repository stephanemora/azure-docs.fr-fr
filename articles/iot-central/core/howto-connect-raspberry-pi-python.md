---
title: Connecter un appareil Raspberry Pi à votre application Azure IoT Central (Python) | Microsoft Docs
description: En tant que développeur d’appareils, comment connecter un appareil Raspberry Pi à votre application Azure IoT Central en utilisant Python.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3daa567a916bd0abeb407028c7d06bd1f2bd464b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454116"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connecter un appareil Raspberry Pi à votre application Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Raspberry Pi à votre application Microsoft Azure IoT Central en utilisant le langage de programmation Python.

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre les étapes décrites dans cet article, vous devez disposer de ce qui suit :

* Une application Azure IoT Central créée à partir du modèle d’application **Application héritée**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Un appareil Raspberry Pi exécutant le système d’exploitation Raspbian. L’appareil Raspberry Pi doit être en mesure de se connecter à Internet. Pour plus d’informations, consultez [Configurer votre appareil Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Pour découvrir la configuration et la connexion à un appareil Raspberry Pi, voir [Prise en main de Raspberry Pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi).

## <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

Dans votre application Azure IoT Central, ajoutez un nouveau modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

- Les données de télémétrie, qui comprennent les mesures suivantes recueillies par l’appareil :
  - Humidité
  - Température
  - Pression
  - Magnétomètre (X, Y, Z)
  - Accéléromètre (X, Y, Z)
  - Gyroscope (X, Y, Z)
- Paramètres
  - Voltage
  - Current
  - Vitesse du ventilateur
  - Bascule IR.
- Propriétés
  - Propriété d’appareil avec numéro gravé
  - Propriété d’emplacement cloud

1. Sélectionnez **+ Nouveau** à partir du ![Modèle d’appareil](media/howto-connect-raspberry-pi-python/adddevicetemplate.png) des modèles d’appareil
   

2. Sélectionnez **Raspberry Pi** et créez le modèle d’appareil Raspberry Pi ![Ajouter un modèle d’appareil](media/howto-connect-raspberry-pi-python/newdevicetemplate.png)

Pour plus d’informations sur la configuration du modèle d’appareil, consultez [Détails de modèle d’appareil Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **Raspberry Pi**. Notez les détails de connexion de l’appareil : **ID de l’étendue**, **ID de l’appareil** et **Clé primaire**. Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurer l’appareil Raspberry Pi

Les étapes suivantes décrivent comment télécharger et configurer l’exemple d’application Python à partir de GitHub. Cet exemple d’application :

* Envoie des valeurs de télémétrie et de propriétés à Azure IoT Central.
* Répond à des modifications de paramètres effectuées dans Azure IoT Central.

1. Connectez-vous à un environnement d’interpréteur de commandes sur votre Raspberry pi, soit à partir du bureau Raspberry pi, soit à distance à l’aide de SSH.

1. Exécutez la commande suivante pour installer le client Python IoT Central :

    ```bash
    pip install iotc
    ```

1. Téléchargez l’exemple de code Python :

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Modifiez le fichier `app.py` que vous avez téléchargé et remplacez les espaces réservés `DEVICE_ID`, `SCOPE_ID` et `PRIMARY/SECONDARY device KEY` par les valeurs de connexion que vous avez notées précédemment. Enregistrez vos modifications.

    > [!TIP]
    > Dans l’interpréteur de commandes sur l’appareil Raspberry Pi, vous pouvez utiliser l’éditeur de texte **nano** ou **vi**.

1. Utilisez la commande suivante pour exécuter cet exemple :

    ```bash
    python app.py
    ```

    Votre Raspberry pi commence à envoyer des mesures de télémétrie à Azure IoT Central.

1. Dans votre application Azure IoT Central, vous pouvez voir comment le code s’exécutant sur l’appareil Raspberry Pi interagit avec l’application :

    * Dans la page **Mesures** de votre appareil réel, vous pouvez voir la télémétrie envoyée depuis l’appareil Raspberry Pi.
    * La page **Propriétés** affiche la propriété d’appareil **Numéro gravé**.
    * Dans la page **Paramètres**, vous modifier les paramètres de l’appareil Raspberry Pi, comme le voltage et la vitesse du ventilateur. Lorsque l’appareil Raspberry Pi accuse réception de la modification, le paramètre s’affiche comme étant **synchronisé**.

## <a name="raspberry-pi-device-template-details"></a>Détails de modèle d’appareil Raspberry Pi

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

| Nom du champ     | Units  | Minimum | Maximale | Nombre de décimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humidité       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pression       | hPa    | 260     | 1 260    | 0              |
| magnetometerX  | mgauss | -1 000   | 1 000    | 0              |
| magnetometerY  | mgauss | -1 000   | 1 000    | 0              |
| magnetometerZ  | mgauss | -1 000   | 1 000    | 0              |
| accelerometerX | mg     | -2 000   | 2000    | 0              |
| accelerometerY | mg     | -2 000   | 2000    | 0              |
| accelerometerZ | mg     | -2 000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2 000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2 000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2 000   | 2000    | 0              |

### <a name="settings"></a>Paramètres

Paramètres numériques

| Nom complet | Nom du champ | Units | Nombre de décimales | Minimum | Maximale | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltage      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Vitesse du ventilateur    | fanSpeed   | TR/MIN   | 0              | 0       | 1 000    | 0       |

Paramètres de bascule

| Nom complet | Nom du champ | Texte pour Activé | Texte pour Désactivé | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ACTIVÉ      | OFF      | Off     |

### <a name="properties"></a>Propriétés

| Type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | nombre    |
| Texte            | Location     | location   | N/A       |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un appareil Raspberry Pi à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
