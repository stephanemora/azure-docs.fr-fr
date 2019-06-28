---
title: Connecter un appareil Raspberry Pi à votre application Azure IoT Central (Python) | Microsoft Docs
description: En tant que développeur d’appareils, comment connecter un appareil Raspberry Pi à votre application Azure IoT Central en utilisant Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: eccc4100c89c971e264b9b915cd17b9f5ce4477b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64405871"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connecter un appareil Raspberry Pi à votre application Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Raspberry Pi à votre application Microsoft Azure IoT Central en utilisant le langage de programmation Python.

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre les étapes décrites dans cet article, vous devez disposer de ce qui suit :

* Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Un appareil Raspberry Pi exécutant le système d’exploitation Raspbian. L’appareil Raspberry Pi doit être en mesure de se connecter à Internet. Pour plus d’informations, consultez [Configurer votre appareil Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Exemple d’application Devkits**

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

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
- properties
  - Propriété d’appareil avec numéro gravé
  - Propriété d’emplacement cloud

Pour plus d’informations sur la configuration du modèle d’appareil, consultez [Détails de modèle d’appareil Raspberry PI](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **Raspberry Pi**. Notez les détails de connexion de l’appareil : **ID de l’étendue**, **ID de l’appareil** et **Clé primaire**. Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurer l’appareil Raspberry Pi

Les étapes suivantes décrivent comment télécharger et configurer l’exemple d’application Python à partir de GitHub. Cet exemple d’application :

* Envoie des valeurs de télémétrie et de propriétés à Azure IoT Central.
* Répond à des modifications de paramètres effectuées dans Azure IoT Central.

Pour configurer l’appareil, [suivez les instructions détaillées sur GitHub.](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md)

1. Une fois l’appareil configuré, il envoie des mesures de télémétrie à Azure IoT Central.
1. Dans votre application Azure IoT Central, vous pouvez voir comment le code s’exécutant sur l’appareil Raspberry Pi interagit avec l’application :

    * Dans la page **Mesures** de votre appareil réel, vous pouvez voir la télémétrie envoyée depuis l’appareil Raspberry Pi.
    * Dans la page **Paramètres**, vous modifier les paramètres de l’appareil Raspberry Pi, comme le voltage et la vitesse du ventilateur. Lorsque l’appareil Raspberry Pi accuse réception de la modification, le paramètre s’affiche comme étant **synchronisé**.

## <a name="raspberry-pi-device-template-details"></a>Détails de modèle d’appareil Raspberry Pi

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

| Nom du champ     | Units  | Minimale | Maximale | Nombre de décimales |
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

| Nom complet | Nom du champ | Units | Nombre de décimales | Minimale | Maximale | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltage      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Vitesse du ventilateur    | fanSpeed   | TR/MIN   | 0              | 0       | 1 000    | 0       |

Paramètres de bascule

| Nom complet | Nom du champ | Texte pour Activé | Texte pour Désactivé | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ACTIVÉ      | ÉTEINT      | Off     |

### <a name="properties"></a>properties

| Type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | number    |
| Texte            | Lieu     | location   | N/A       |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un appareil Raspberry Pi à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
