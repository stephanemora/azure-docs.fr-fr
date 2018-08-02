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
ms.openlocfilehash: a3d6ad9f2f442481908bc02252fcc8ab1a74419e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205586"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connecter un appareil Raspberry Pi à votre application Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Raspberry Pi à votre application Microsoft Azure IoT Central en utilisant le langage de programmation Python.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

* Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer votre application Azure IoT Central](howto-create-application.md).
* Un appareil Raspberry Pi exécutant le système d’exploitation Raspbian. Vous avez besoin d’un écran, d’un clavier et d’une souris connectés à votre appareil Raspberry Pi pour accéder à l’environnement de l’interface graphique utilisateur. L’appareil Raspberry Pi doit être en mesure de [se connecter à Internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Éventuellement, une carte de module complémentaire [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) pour l’appareil Raspberry Pi. Cette carte collecte les données de télémétrie à partir de différents capteurs et les envoie à votre application Azure IoT Central. Si vous n’avez pas de carte **Sense Hat**, vous pouvez utiliser à la place un émulateur.

## <a name="sample-devkits-application"></a>**Exemple d’application Devkits**

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes : 

- Données de télémétrie qui contiennent les mesures d’**humidité**, de **température**, de **pression**, du **magnétomètre** (mesurées le long des axes X, Y et Z), de l’**accéléromètre** (mesurées le long des axes X, Y et Z) et du **gyroscope** (mesurées le long des axes X, Y et Z) de l’appareil.
- Paramètres montrant la **tension**, le **courant**, la **vitesse du ventilateur** et un bouton bascule **Infrarouge**.
- Propriétés contenant la propriété d’appareil **Numéro gravé** et la propriété cloud **Emplacement**.


Pour plus d’informations sur la configuration du modèle d’appareil, consultez [Détails de modèle d’appareil Raspberry PI](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).
    

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **Raspberry Pi** et prenez note de la chaîne de connexion de l’appareil. Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurer l’appareil Raspberry Pi

Les étapes suivantes décrivent comment télécharger et configurer l’exemple d’application Python à partir de GitHub. Cet exemple d’application :

* Envoie des valeurs de télémétrie et de propriétés à Azure IoT Central.
* Répond à des modifications de paramètres effectuées dans Azure IoT Central.

> [!NOTE]
> Pour plus d’informations sur l’exemple Python pour Raspberry Pi, consultez le fichier [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) sur GitHub.

1. Utilisez le navigateur web du Bureau Raspberry Pi pour accéder à la page des [versions du microprogramme pour Azure IoT Central](https://github.com/Microsoft/microsoft-iot-central-firmware/releases).

1. Téléchargez le fichier zip qui contient le microprogramme le plus récent vers votre dossier de base de l’appareil Raspberry Pi. Le nom de fichier se présente sous la forme `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Pour décompresser le fichier du microprogramme, utilisez le **Gestionnaire de fichiers** du Bureau Raspberry Pi. Cliquez avec le bouton droit sur le fichier zip et choisissez **Extraire ici**. Cette opération crée un dossier nommé `RaspberryPi-IoTCentral-X.X.X` dans votre dossier de base.

1. Si vous n’avez de carte **Sense Hat** attachée à votre appareil Raspberry Pi, vous devez activer l’émulateur :
    1. Dans **Gestionnaire de fichiers**, dans le dossier `RaspberryPi-IoTCentral-X.X.X`, cliquez avec le bouton droit sur le fichier **config.iot** et choisissez **Éditeur de texte**.
    1. Remplacez la ligne `"simulateSenseHat": false,` par `"simulateSenseHat": true,`.
    1. Enregistrez les modifications et fermez l’**Éditeur de texte**.

1. Démarrez une session de **Terminal** et utilisez la commande `cd` pour accéder au dossier que vous avez créé à l’étape précédente.

1. Pour démarrer l’exécution de l’exemple d’application, tapez `./start.sh` dans la fenêtre **Terminal**. Si vous utilisez **l’émulateur Sense HAT**, son interface graphique utilisateur apparaît. Vous pouvez utiliser l’interface graphique utilisateur pour modifier les valeurs de télémétrie envoyées à votre application Azure IoT Central.

1. La fenêtre **Terminal** affiche un message qui ressemble à ceci : `Device information being served at http://192.168.0.60:8080`. L’URL peut être différente dans votre environnement. Copiez l’URL et utilisez le navigateur web pour accéder à la page de configuration :

    ![Configurer l’appareil](media/howto-connect-raspberry-pi-python/configure.png)

1. Entrez la chaîne de connexion de l’appareil dont vous avez pris note quand vous avez ajouté un appareil réel à votre application Azure IoT Central. Choisissez ensuite **Configurer l’appareil**. Vous voyez un message **Appareil configuré, votre appareil devrait commencer à envoyer des données à Azure IoT Central dès maintenant**.

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
