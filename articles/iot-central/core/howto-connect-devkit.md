---
title: Connecter un appareil DevKit à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 270f92365823fb0f9378a9daae77dbbe08b53b14
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435080"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Cet article vous explique comment, en tant que développeur d’appareils, vous pouvez connecter un appareil DevKit IoT MXChip (DevKit) à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des ressources suivantes :

1. Une application Azure IoT Central créée à partir du modèle d’application **Application héritée**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
1. Un appareil DevKit. Pour acheter un appareil DevKit, accédez à [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

Dans votre application Azure IoT Central, ajoutez un nouveau modèle d’appareil **MXChip** qui définit les caractéristiques d’appareil suivantes :

- données de télémétrie **d’humidité**, de **température**, de **pression**, du **magnétomètre** (mesurées le long des axes X, Y et Z), de **l’accéléromètre** (mesurées le long des axes X, Y et Z) et du **gyroscope** (mesurées le long des axes X, Y et Z) ;
- mesure de **l’état de l’appareil** ;
- mesure d’événement pour **Bouton B enfoncé** ;
- paramètres de **tension**, **courant**, **vitesse du ventilateur** et bouton bascule **Infrarouge** ;
- propriétés d’appareil **Numéro gravé** et **Emplacement de l’appareil**, qui est une propriété d’emplacement ;
- propriété cloud **Fabriqué dans** ;
- commandes **Écho** et **Compte à rebours**. Quand un appareil réel reçoit une commande **Écho**, la valeur envoyée est affichée sur l’écran de l’appareil. Quand un appareil réel reçoit une commande **Compte à rebours**, la LED parcourt un modèle, puis l’appareil renvoie les valeurs de compte à rebours à IoT Central.

1. Sélectionnez **+ Nouveau** à partir du ![Modèle d’appareil](media/howto-connect-devkit/adddevicetemplate.png) des modèles d’appareil
   

2. Sélectionnez **MXChip** et créez le modèle d’appareil MXChip ![Ajouter un modèle d’appareil](media/howto-connect-devkit/newtemplate.png)

Pour plus d’informations sur la configuration, consultez [Détails du modèle d’appareil MXChip](#mxchip-device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

### <a name="get-your-device-connection-details"></a>Obtenir des détails de la connexion de votre appareil

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **MXChip** et notez les détails de la connexion de l’appareil : **ID de portée, ID d’appareil et Clé primaire** :

1. Ajoutez un **appareil réel** à partir d’Appareils, puis sélectionnez **+ Nouveau > Réel** pour ajouter un appareil réel.

    * Entrez un **ID d’appareil** en minuscules ou utilisez **l’ID d’appareil** suggéré.
    * Entrez un **Nom de l’appareil** ou utilisez le nom suggéré.

    ![Ajout d’un appareil](media/howto-connect-devkit/add-device.png)

1. Pour obtenir des informations détaillées sur la connexion de l’appareil, telles que les paramètres **ID de portée**, **ID d’appareil** et **Clé primaire**, sélectionnez **Connecter** sur la page de l’appareil.

    ![Informations de connexion](media/howto-connect-devkit/device-connect.png)

1. Prenez note des détails de la connexion. Vous êtes temporairement déconnecté d’Internet lorsque vous préparez votre appareil DevKit à l’étape suivante.

### <a name="prepare-the-devkit-device"></a>Préparer l’appareil DevKit

Si vous avez déjà utilisé l’appareil et souhaitez le reconfigurer de façon à utiliser un autre réseau Wi-Fi, une autre chaîne de connexion ou une autre mesure de télémétrie, appuyez simultanément sur les boutons **A** et **B**. Si cela ne fonctionne pas, appuyez sur le bouton **Réinitialiser**, puis réessayez.

#### <a name="to-prepare-the-devkit-device"></a>Pour préparer l'appareil DevKit

1. Téléchargez la dernière version du microprogramme Azure IoT Central prédéfini pour MXChip à partir de la page [Releases](https://aka.ms/iotcentral-docs-MXChip-releases) de GitHub.
1. Connectez l’appareil DevKit à votre machine de développement à l’aide d’un câble USB. Dans Windows, une fenêtre Explorateur de fichiers s’ouvre sur un lecteur mappé au stockage de l’appareil DevKit. Par exemple, le lecteur peut s’appeler **AZ3166 (d)** .
1. Faites glisser le fichier **iotCentral.bin** jusqu’à la fenêtre du lecteur. Une fois la copie effectuée, l’appareil redémarre avec le nouveau microprogramme.

1. Pendant le redémarrage de l’appareil DevKit, voici l’écran que vous obtenez :

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Si l’écran affiche autre chose, réinitialisez l’appareil et appuyez simultanément sur les boutons **A** et **B** de l’appareil afin de le redémarrer.

1. L’appareil est maintenant en mode AP (point d’accès). Vous pouvez vous connecter à ce point d’accès Wi-Fi à partir de votre ordinateur ou appareil mobile.

1. Sur votre ordinateur, téléphone ou tablette, connectez-vous au réseau Wi-Fi dont le nom est affiché sur l’écran de l’appareil. Quand vous vous connectez à ce réseau, vous n’avez pas accès à Internet. Il s’agit d’un état normal. Vous êtes connecté uniquement à ce réseau pendant un bref laps de temps, le temps de configurer l’appareil.

1. Ouvrez votre navigateur web et accédez à [http://192.168.0.1/start](http://192.168.0.1/start). La page web suivante s’affiche à l’écran :

    ![Page de configuration de l’appareil](media/howto-connect-devkit/configpage.png)

    Dans la page web, entrez :
    - Le nom de votre réseau Wi-Fi
    - Le mot de passe de votre réseau Wi-Fi
    - Le code secret indiqué sur l’écran de l’appareil
    - Les informations de connexion **ID de portée**, **ID d’appareil** et **Clé primaire** de votre appareil (vous devez déjà les avoir enregistrés en suivant les étapes)
    - Sélectionnez toutes les mesures de télémétrie disponibles

1. Après avoir choisi **Configurer l’appareil**, cette page apparaît :

    ![Appareil configuré](media/howto-connect-devkit/deviceconfigured.png)

1. Appuyez sur le bouton de **réinitialisation** de votre appareil.

## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

Pendant le redémarrage de l’appareil DevKit, l’écran de l’appareil affiche les informations suivantes :

* Nombre de messages de télémétrie envoyés.
* Nombre d’échecs.
* Nombre de propriétés souhaitées reçues et nombre de propriétés signalés envoyées.

> [!NOTE]
> Si l’appareil semble être en boucle lorsqu’il essaie de se connecter, vérifiez s’il est **bloqué** dans IoT Central, puis **débloquez**-le pour qu’il se connecte à l’application.

Secouez l’appareil pour envoyer une propriété rapportée. L’appareil envoie un nombre aléatoire pour la propriété de l’appareil **Numéro gravé**.

Vous pouvez consulter les mesures de télémétrie et les valeurs des propriétés signalées et configurer les paramètres dans Azure IoT Central :

1. Utilisez **Appareils** pour accéder à la page **Mesures** de l’appareil MXChip réel que vous avez ajouté :

    ![Accéder à l’appareil réel](media/howto-connect-devkit/realdevicenew.png)

1. Dans la page **Mesures**, vous pouvez examiner les données de télémétrie en provenance de l’appareil MXChip :

    ![Afficher les données de télémétrie de l’appareil réel](media/howto-connect-devkit/devicetelemetrynew.png)

1. Dans la page **Propriétés**, vous pouvez voir le dernier numéro gravé et l’emplacement d’appareil signalés par l’appareil :

    ![Voir les propriétés de l’appareil](media/howto-connect-devkit/devicepropertynew.png)

1. Dans la page **Propriétés**, vous pouvez mettre à jour les paramètres de l’appareil MXChip :

    ![Afficher les paramètres de l’appareil](media/howto-connect-devkit/devicesettingsnew.png)

1. Sur la page **Commandes**, vous pouvez appeler les commandes **Écho** et **Compte à rebours** :

    ![Appeler des commandes](media/howto-connect-devkit/devicecommands.png)

1. Dans la page **Tableau de bord**, vous pouvez voir la carte de l’emplacement

    ![Afficher le tableau de bord d’un appareil](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Télécharger le code source

Si vous souhaitez explorer et modifier le code de l’appareil, vous pouvez le télécharger à partir de GitHub. Si vous envisagez de modifier le code, suivez ces instructions pour [préparer l’environnement de développement](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) du système d’exploitation de votre ordinateur.

Pour télécharger le code source, exécutez la commande suivante sur votre ordinateur de bureau :

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

La commande précédente télécharge le code source dans un dossier appelé `iot-central-firmware`.

> [!NOTE]
> Si **git** n’est pas installé dans votre environnement de développement, vous pouvez le télécharger à partir de [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Vérifier le code

Utilisez Visual Studio Code pour ouvrir le dossier `MXCHIP/mxchip_advanced` dans le dossier `iot-central-firmware` :

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Pour voir comment les données de télémétrie sont envoyées à l’application Azure IoT Central, ouvrez le fichier **telemetry.cpp** dans le dossier `src` :

- La fonction `TelemetryController::buildTelemetryPayload` crée la charge utile de télémétrie JSON en utilisant les données issues des capteurs de l’appareil.

- La fonction `TelemetryController::sendTelemetryPayload` appelle `sendTelemetry` dans **AzureIOTClient.cpp** pour envoyer la charge utile JSON au hub IoT qu’utilise votre application Azure IoT Central.

Pour voir comment les valeurs des propriétés sont rapportées à l’application Azure IoT Central, ouvrez le fichier **telemetry.cpp** dans le dossier `src` :

- La fonction `TelemetryController::loop` envoie la propriété rapportée **d’emplacement** toutes les 30 secondes environ. Elle utilise la fonction `sendReportedProperty` dans le fichier source **AzureIOTClient.cpp**.

- La fonction `TelemetryController::loop` envoie la propriété rapportée **dieNumber** quand l’accéléromètre de l’appareil détecte un double appui. Elle utilise la fonction `sendReportedProperty` dans le fichier source **AzureIOTClient.cpp**.

Pour voir comment l’appareil répond aux commandes appelées à partir de l’application IoT Central, ouvrez le fichier **registeredMethodHandlers.cpp** contenu dans le dossier `src` :

- La fonction **dmEcho** est le gestionnaire de la commande **echo**. Elle affiche la **displayedValue** indiquée dans la charge utile sur l’écran de l’appareil.

- La fonction **dmCountdown** est le gestionnaire de la commande **countdown**. Elle modifie la couleur de la LED de l’appareil et utilise une propriété rapportée pour renvoyer la valeur de compte à rebours à l’application IoT Central. La propriété rapportée a le même nom que la commande. La fonction utilise la fonction `sendReportedProperty` dans le fichier source **AzureIOTClient.cpp**.

Le code du fichier source **AzureIOTClient.cpp** utilise les fonctions des [Kits de développement logiciel (SDK) Microsoft Azure IoT et des bibliothèques pour C](https://github.com/Azure/azure-iot-sdk-c) pour interagir avec IoT Hub.

Pour plus d’informations sur la façon de modifier, générer et charger l’exemple de code sur votre appareil, consultez le fichier **readme.md** dans le dossier`MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Détails du modèle d’appareil MXChip

Une application créée à partir du modèle d’application Exemples de Devkits comprend un modèle d’appareil MXChip qui présente les caractéristiques suivantes :

### <a name="measurements"></a>Mesures

#### <a name="telemetry"></a>Télémétrie

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

#### <a name="states"></a>États 
| Name          | Nom complet   | NORMAL | AVERTISSEMENT | DANGER | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | État de l’appareil   | Vert  | Orange  | Rouge    | 

#### <a name="events"></a>Événements 
| Name             | Nom complet      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Bouton B enfoncé  | 

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
| Propriété d’appareil | Emplacement de l’appareil   | location  | location    |
| Texte            | Fabriqué dans     | manufacturedIn   | N/A       |

### <a name="commands"></a>Commandes

| Nom complet | Nom du champ | Type de retour | Nom d’affichage du champ d’entrée | Nom du champ d’entrée | Type de champ d’entrée |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Écho         | echo       | text        | Valeur à afficher         | displayedValue   | text             |
| Compte à rebours    | countdown  | nombre      | Compter à partir de               | countFrom        | nombre           |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un DevKit IoT MXChip à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
