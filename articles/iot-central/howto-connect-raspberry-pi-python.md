---
title: Connecter un appareil Raspberry Pi à votre application Azure IoT Central (Python) | Microsoft Docs
description: En tant que développeur d’appareils, comment connecter un appareil Raspberry Pi à votre application Azure IoT Central en utilisant Python.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9f39832b50ed983e7d8a0bfc0a06366870717fa3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051983"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connecter un appareil Raspberry Pi à votre application Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Raspberry Pi à votre application Microsoft Azure IoT Central en utilisant le langage de programmation Python.

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre les étapes décrites dans cet article, vous devez disposer de ce qui suit :

* Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Un appareil Raspberry Pi exécutant le système d’exploitation Raspbian. Vous avez besoin d’un écran, d’un clavier et d’une souris connectés à votre appareil Raspberry Pi pour accéder à l’environnement de l’interface graphique utilisateur. L’appareil Raspberry Pi doit être en mesure de [se connecter à Internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Éventuellement, une carte de module complémentaire [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) pour l’appareil Raspberry Pi. Cette carte collecte les données de télémétrie à partir de différents capteurs et les envoie à votre application Azure IoT Central. Si vous n’avez pas de carte **Sense Hat**, vous pouvez utiliser à la place un émulateur (fourni avec l’image Raspberry Pi).

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

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **Raspberry Pi** et notez les informations de connexion de l’appareil (**ID d’étendue, ID d’appareil et Clé primaire**). Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).


### <a name="configure-the-raspberry-pi"></a>Configurer l’appareil Raspberry Pi

Les étapes suivantes décrivent comment télécharger et configurer l’exemple d’application Python à partir de GitHub. Cet exemple d’application :

* Envoie des valeurs de télémétrie et de propriétés à Azure IoT Central.
* Répond à des modifications de paramètres effectuées dans Azure IoT Central.

Pour configurer l’appareil, [suivez les instructions détaillées sur GitHub.](https://aka.ms/iotcentral-docs-Raspi-releases)


> [!NOTE]
> Pour plus d’informations sur l’exemple Python pour Raspberry Pi, consultez le fichier [README](https://aka.ms/iotcentral-docs-Raspi-releases) sur GitHub.


1. Une fois configuré, votre appareil devrait bientôt commencer à envoyer des données à Azure IoT Central.
1. Dans votre application Azure IoT Central, vous voyez comment le code s’exécutant sur l’appareil Raspberry Pi interagit avec l’application :

    * Dans la page **Mesures** de votre appareil réel, vous pouvez voir la télémétrie envoyée depuis l’appareil Raspberry Pi. Si vous utilisez **l’émulateur Sense HAT**, vous pouvez modifier les valeurs de télémétrie dans l’interface graphique utilisateur de l’appareil Raspberry Pi.
    * Dans la page **Propriétés**, vous pouvez voir la valeur de la propriété **Numéro gravé**.
    * Dans la page **Paramètres**, vous pouvez changer différents paramètres de l’appareil Raspberry Pi, comme le voltage et la vitesse du ventilateur. Quand l’appareil Raspberry Pi accuse réception de la modification, le paramètre s’affiche comme étant **synchronisé** dans Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Détails de modèle d’appareil Raspberry PI

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

| type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | number    |
| Texte            | Lieu     | location   | N/A       |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un appareil Raspberry Pi à votre application Azure IoT Central, voici les étapes suivantes suggérées :

* [Connecter une application cliente Node.js générique à Azure IoT Central](howto-connect-nodejs.md)
