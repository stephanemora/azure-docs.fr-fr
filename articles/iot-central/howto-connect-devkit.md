---
title: Connecter un appareil DevKit à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 44af0ccab45f1335d9dfec06287303a34391eded
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113195"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central

Cet article vous explique comment, en tant que développeur d’appareils, vous pouvez connecter un appareil DevKit IoT MXChip (DevKit) à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
1. Un appareil DevKit. Pour acheter un appareil DevKit, accédez à [MXChip IoT DevKit](http://mxchip.com/az3166).

## <a name="sample-devkits-application"></a>Exemple d'application Devkits

Une application créée à partir du modèle d’application **Exemples de Devkits** comprend un modèle d’appareil **MXChip** qui présente les caractéristiques suivantes :

- Données de télémétrie qui contiennent les mesures d'**humidité**, de **température**, de **pression**, du **magnétomètre** (mesurées le long des axes X, Y et Z), de l'**accéléromètre** (mesurées le long des axes X, Y et Z) et du **gyroscope** (mesurées le long des axes X, Y et Z) de l'appareil.
- État qui contient un exemple de mesure pour l’**État de l’appareil**.
- Mesure d’événement avec un événement **Bouton B enfoncé**. 
- Paramètres montrant la **tension**, le **courant**, la **vitesse du ventilateur** et un bouton bascule **Infrarouge**.
- Propriétés contenant la propriété de l’appareil **Numéro gravé** et l’**Emplacement de l’appareil**, qui est une propriété d’emplacement, ainsi qu’une propriété cloud **Fabriqué en**. 

Pour plus d’informations sur la configuration, consultez [Détails du modèle d’appareil MXChip](#mxchip-device-template-details).


## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **MXChip** et notez les informations de connexion de l’appareil (**ID d’étendue, ID d’appareil, et Clé primaire**).

1. Ajouter un **appareil réel** dans Device Explorer, sélectionnez **+ Nouveau > réel** pour ajouter un appareil réel.

   * Entrez l’ID d’appareil **<span style="color:Red">(doit être en minuscules)</span>** ou utilisez l’ID d’appareil suggéré.
   * Entrez le nom de l’appareil ou utilisez le nom suggéré

     ![Ajout d’un appareil](media/howto-connect-devkit/add-device.png)

1. Obtenir des informations de connexion telles que **clé étendue ID, ID de l’appareil et principal** pour l’appareil ajouté en sélectionnant **Connect** sur la page de l’appareil.

    ![Informations de connexion](media/howto-connect-devkit/device-connect.png)

1. Veillez à enregistrer ces informations, car vous serez momentanément déconnecté d'Internet lorsque vous préparerez l'appareil DevKit.

### <a name="prepare-the-devkit-device"></a>Préparer l’appareil DevKit

> [!NOTE]
> Si vous avez précédemment utilisé l’appareil et que vous avez des informations d’identification Wi-Fi stockées, et si vous voulez reconfigurer l’appareil de façon à utiliser un autre réseau Wi-Fi, une autre chaîne de connexion ou une autre mesure de télémétrie, appuyez simultanément sur les boutons **A** et **B** de la carte. Si cela ne fonctionne pas, appuyez sur le bouton de **réinitialisation**, puis réessayez.

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

1. Sur votre ordinateur, téléphone ou tablette, connectez-vous au réseau Wi-Fi dont le nom est affiché sur l’écran de l’appareil. Quand vous vous connectez à ce réseau, vous n’avez pas accès à Internet. Il s’agit d’un état normal. Vous êtes connecté uniquement à ce réseau pendant un bref laps de temps, le temps de configurer l’appareil.

1. Ouvrez votre navigateur web et accédez à [http://192.168.0.1/start](http://192.168.0.1/start). La page web suivante s’affiche à l’écran :

    ![Page de configuration de l’appareil](media/howto-connect-devkit/configpage.png)

    Dans la page web : 
    - ajoutez le nom de votre réseau Wi-Fi 
    - le mot de passe de votre réseau Wi-Fi
    - le code PIN (PIN CODE) indiqué sur l’écran de l’appareil 
    - les informations de connexion **ID d’étendue, ID d’appareil, et Clé primaire** de votre appareil (vous devez déjà les avoir enregistrées en suivant les étapes)      
    - Sélectionnez toutes les mesures de télémétrie disponibles. 

1. Après avoir choisi **Configurer l’appareil**, cette page apparaît :

    ![Appareil configuré](media/howto-connect-devkit/deviceconfigured.png)

1. Appuyez sur le bouton de **réinitialisation** de votre appareil.


## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

Pendant le redémarrage de l’appareil DevKit, l’écran de l’appareil affiche les informations suivantes :

* Nombre de messages de télémétrie envoyés.
* Nombre d’échecs.
* Nombre de propriétés souhaitées reçues et nombre de propriétés signalés envoyées.

> [!NOTE]
> Si l’appareil semble être en boucle lors de la connexion, vérifiez si l’appareil est *bloqué* dans IoT Central, et *Débloquez* l’appareil pour qu’il se connecte à l’application.

Le fait de secouer l’appareil a pour effet d’augmenter le nombre de propriétés signalées envoyées. L’appareil envoie un nombre aléatoire pour la propriété de l’appareil **Numéro gravé**.

Vous pouvez consulter les mesures de télémétrie et les valeurs des propriétés signalées et configurer les paramètres dans Azure IoT Central :

1. Utilisez l’**Explorateur d’appareils** pour accéder à la page **Mesures** pour l’appareil MXChip réel que vous avez ajouté :

    ![Accéder à l’appareil réel](media/howto-connect-devkit/realdevicenew.png)

1. Dans la page **Mesures**, vous pouvez examiner les données de télémétrie en provenance de l’appareil MXChip :

    ![Afficher les données de télémétrie de l’appareil réel](media/howto-connect-devkit/devicetelemetrynew.png)

1. Dans la page **Propriétés**, vous pouvez voir le dernier numéro gravé et l’emplacement d’appareil signalés par l’appareil :

    ![Voir les propriétés de l’appareil](media/howto-connect-devkit/devicepropertynew.png)

1. Dans la page **Propriétés**, vous pouvez mettre à jour les paramètres de l’appareil MXChip :

    ![Afficher les paramètres de l’appareil](media/howto-connect-devkit/devicesettingsnew.png)

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

Utilisez Visual Studio Code, qui a été installé pendant la préparation de votre environnement de développement, pour ouvrir le dossier `AZ3166` à l’intérieur du dossier `iot-central-firmware` : 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Pour voir comment les données de télémétrie sont envoyées à l’application Azure IoT Central, ouvrez le fichier **main_telemetry.cpp** dans le dossier source.

La fonction `buildTelemetryPayload` crée la charge utile de télémétrie JSON en utilisant les données issues des capteurs de l’appareil.

La fonction `sendTelemetryPayload` appelle `sendTelemetry` dans **iotHubClient.cpp** pour envoyer la charge utile JSON au hub IoT qu’utilise votre application Azure IoT Central.

Pour voir comment les valeurs des propriétés sont signalées à l’application Azure IoT Central, ouvrez le fichier **main_telemetry.cpp** dans le dossier source.

La fonction `telemetryLoop` envoie la propriété signalée **doubleTap** quand l’accéléromètre détecte un double appui. Elle utilise la fonction `sendReportedProperty` dans le fichier source **iotHubClient.cpp**.

Le code du fichier source **iotHubClient.cpp** utilise les fonctions des [kits SDK Microsoft Azure IoT et des bibliothèques pour C](https://github.com/Azure/azure-iot-sdk-c) pour interagir avec IoT Hub.

Pour plus d’informations sur la façon de modifier, générer et charger l’exemple de code sur votre appareil, consultez le fichier **readme.md** dans le dossier`AZ3166`.

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
| Nom          | Nom complet   | NORMAL | AVERTISSEMENT | DANGER | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | État de l’appareil   | Vert  | Orange  | Rouge    | 

#### <a name="events"></a>Événements 
| Nom             | Nom complet      | 
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

| Type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | number    |
| Propriété d’appareil | Emplacement de l’appareil   | location  | location    |
| Texte            | Fabriqué dans     | manufacturedIn   | S.O.       |



## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment connecter un appareil DevKit à votre application Azure IoT Central, voici les étapes suivantes suggérées :

* [Préparer et connecter un Raspberry Pi](howto-connect-raspberry-pi-python.md)
