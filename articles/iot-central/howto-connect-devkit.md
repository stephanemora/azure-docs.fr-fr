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
ms.openlocfilehash: 82222dd927f46761941a6a750d96222cc626e71b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006180"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central

Cet article vous explique comment, en tant que développeur d’appareils, vous pouvez connecter un appareil DevKit IoT MXChip (DevKit) à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes décrites dans cet article, vous devez les ressources suivantes :

1. Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
1. Un appareil DevKit. Pour acheter un appareil DevKit, accédez à [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Exemple d'application Devkits

Une application créée à partir de la **exemple Devkits** ce modèle d’application inclut un **MXChip** modèle de périphérique qui définit les caractéristiques d’appareil suivantes :

- Mesures de télémétrie pour **humidité**, **température**, **pression**, **magnétomètre** (mesuré le long de X, Y, l’axe Z), **Accéléromètre** (mesuré le long de X, Y, l’axe Z), et **Gyroscope** (mesuré le long de X, Y, l’axe Z).
- Mesure de l’état **état de l’appareil**.
- Mesure des événements pour **bouton B enfoncé**.
- Paramètres pour **Voltage**, **actuel**, **vitesse du ventilateur**et un **IR** activer/désactiver.
- Propriétés de l’appareil **meurent numéro** et **emplacement de l’appareil**, qui est une propriété d’emplacement.
- Propriété en nuage **fabriqués dans**.
- Commandes **Echo** et **compte à rebours**. Quand un appareil réel reçoit un **Echo** commande, il affiche la valeur envoyée sur l’affichage du périphérique. Quand un appareil réel reçoit un **compte à rebours** commande, les cycles de LED via un modèle, et l’appareil envoie des valeurs de compte à rebours à IoT Central.

Pour plus d’informations sur la configuration, consultez [détails du modèle MXChip appareil](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Ajouter un appareil réel

### <a name="get-your-device-connection-details"></a>Obtenir des détails de la connexion de votre appareil

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir de la **MXChip** modèle d’appareil et notez les détails de connexion d’appareil : **Étendue d’ID, ID de l’appareil et la clé primaire**:

1. Ajouter un **appareil réel** dans Device Explorer, sélectionnez **+ Nouveau > réel** pour ajouter un appareil réel.

    * Entrez un caractère minuscule **ID d’appareil**, ou utilisez le texte suggéré **ID d’appareil**.
    * Entrez un **nom de l’appareil**, ou utilisez le nom suggéré

    ![Ajout d’un appareil](media/howto-connect-devkit/add-device.png)

1. Pour obtenir des informations de connexion, de l’appareil **ID de portée**, **ID d’appareil**, et **clé primaire**, sélectionnez **Connect** sur la page de l’appareil.

    ![Informations de connexion](media/howto-connect-devkit/device-connect.png)

1. Prenez note des détails de connexion. Vous êtes temporairement déconnecté d’internet lorsque vous préparez votre appareil DevKit à l’étape suivante.

### <a name="prepare-the-devkit-device"></a>Préparer l’appareil DevKit

Si vous avez déjà utilisé l’appareil et vous souhaitez reconfigurer pour qu’il utilise un autre réseau Wi-Fi, la chaîne de connexion ou la mesure de télémétrie, appuyez sur les deux le **A** et **B** boutons en même temps. Si cela ne fonctionne pas, appuyez sur **réinitialiser** bouton, puis réessayez.

#### <a name="to-prepare-the-devkit-device"></a>Pour préparer l'appareil DevKit

1. Téléchargez la dernière version du microprogramme Azure IoT Central prédéfini pour MXChip à partir de la page [Releases](https://aka.ms/iotcentral-docs-MXChip-releases) de GitHub.
1. Connectez l’appareil DevKit à votre machine de développement à l’aide d’un câble USB. Dans Windows, une fenêtre Explorateur de fichiers s’ouvre sur un lecteur mappé au stockage de l’appareil DevKit. Par exemple, le lecteur peut s’appeler **AZ3166 (d)**.
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

1. Sur votre ordinateur, téléphone ou tablette, connectez-vous au réseau Wi-Fi dont le nom est affiché sur l’écran de l’appareil. Lorsque vous vous connectez à ce réseau, vous n’avez accès à internet. Cet état est prévu et que vous êtes connecté uniquement à ce réseau pendant une courte période pendant la configuration de l’appareil.

1. Ouvrez votre navigateur web et accédez à [http://192.168.0.1/start](http://192.168.0.1/start). La page web suivante s’affiche à l’écran :

    ![Page de configuration de l’appareil](media/howto-connect-devkit/configpage.png)

    Dans la page web, entrez :
    - Le nom de votre réseau Wi-Fi
    - votre mot de passe réseau Wi-Fi
    - Le code PIN qui apparaît sur l’affichage du périphérique
    - Les détails de connexion **ID de portée**, **ID d’appareil**, et **clé primaire** de votre appareil (vous devez avez déjà enregistré cette suivant les étapes)
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
> Si l’appareil pour effectuer une boucle quand il tente de se connecter, vérifiez si l’appareil est **bloqué** dans IoT Central, et **Unblock** l’appareil pour se connecter à l’application.

Secouez l’appareil pour envoyer une propriété signalée. L’appareil envoie un nombre aléatoire pour la propriété de l’appareil **Numéro gravé**.

Vous pouvez consulter les mesures de télémétrie et les valeurs des propriétés signalées et configurer les paramètres dans Azure IoT Central :

1. Utilisez l’**Explorateur d’appareils** pour accéder à la page **Mesures** pour l’appareil MXChip réel que vous avez ajouté :

    ![Accéder à l’appareil réel](media/howto-connect-devkit/realdevicenew.png)

1. Dans la page **Mesures**, vous pouvez examiner les données de télémétrie en provenance de l’appareil MXChip :

    ![Afficher les données de télémétrie de l’appareil réel](media/howto-connect-devkit/devicetelemetrynew.png)

1. Dans la page **Propriétés**, vous pouvez voir le dernier numéro gravé et l’emplacement d’appareil signalés par l’appareil :

    ![Voir les propriétés de l’appareil](media/howto-connect-devkit/devicepropertynew.png)

1. Dans la page **Propriétés**, vous pouvez mettre à jour les paramètres de l’appareil MXChip :

    ![Afficher les paramètres de l’appareil](media/howto-connect-devkit/devicesettingsnew.png)

1. Sur le **commandes** page, vous pouvez appeler la **Echo** et **compte à rebours** commandes :

    ![Commandes d’appel](media/howto-connect-devkit/devicecommands.png)

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

Utilisez Visual Studio Code pour ouvrir le `MXCHIP/mxchip_advanced` dossier dans le `iot-central-firmware` dossier :

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Pour voir comment les données de télémétrie sont envoyée à l’application Azure IoT Central, ouvrez le **telemetry.cpp** de fichiers dans le `src` dossier :

- La fonction `TelemetryController::buildTelemetryPayload` crée la charge utile de télémétrie JSON en utilisant les données issues des capteurs de l’appareil.

- La fonction `TelemetryController::sendTelemetryPayload` appels `sendTelemetry` dans le **AzureIOTClient.cpp** pour envoyer la charge utile JSON à IoT Hub à votre application utilise Azure IoT Central.

Pour voir la façon dont les valeurs de propriété sont signalées à l’application Azure IoT Central, ouvrez le **telemetry.cpp** de fichiers dans le `src` dossier :

- La fonction `TelemetryController::loop` envoie le **emplacement** propriété signalée environ toutes les 30 secondes. Il utilise le `sendReportedProperty` fonctionner dans le **AzureIOTClient.cpp** fichier source.

- La fonction `TelemetryController::loop` envoie le **dieNumber** propriété signalée lors de l’accéléromètre appareil détecte un double clic. Il utilise le `sendReportedProperty` fonctionner dans le **AzureIOTClient.cpp** fichier source.

Pour voir comment l’appareil répond aux commandes appelées à partir de l’application IoT Central, ouvrez le **registeredMethodHandlers.cpp** de fichiers dans le `src` dossier :

- Le **dmEcho** (fonction) est le gestionnaire pour le **echo** commande. Il montre le **displayedValue** classé dans la charge utile sur l’écran du périphérique.

- Le **dmCountdown** (fonction) est le gestionnaire pour le **compte à rebours** commande. Il modifie la couleur du voyant de l’appareil et utilise une propriété signalée à renvoyer la valeur du compte à rebours à l’application IoT Central. La propriété signalée a le même nom que la commande. La fonction utilise le `sendReportedProperty` fonctionner dans le **AzureIOTClient.cpp** fichier source.

Le code dans le **AzureIOTClient.cpp** fichier source utilise des fonctions à partir de la [Microsoft Azure IoT SDK et bibliothèques pour C](https://github.com/Azure/azure-iot-sdk-c) pour interagir avec IoT Hub.

Pour plus d’informations sur la façon de modifier, générer et charger l’exemple de code sur votre appareil, consultez le fichier **readme.md** dans le dossier`MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Détails du modèle d’appareil MXChip

Une application créée à partir du modèle d’application Exemples de Devkits comprend un modèle d’appareil MXChip qui présente les caractéristiques suivantes :

### <a name="measurements"></a>Mesures

#### <a name="telemetry"></a>Télémétrie

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
| Propriété d’appareil | Emplacement de l’appareil   | location  | location    |
| Text            | Fabriqué dans     | manufacturedIn   | S.O.       |

### <a name="commands"></a>Commandes

| Nom complet | Nom du champ | Type de retour | Nom complet du champ d’entrée | Nom du champ d’entrée | Type de champ d’entrée |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo       | text        | valeur à afficher         | displayedValue   | text             |
| compte à rebours    | countdown  | number      | Nombre à partir de               | countFrom        | number           |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un Raspberry Pi à votre application Azure IoT Central, l’étape suivante suggérée consiste à apprendre comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
