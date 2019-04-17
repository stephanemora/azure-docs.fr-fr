---
title: Connecter un appareil Windows IoT Core à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617850"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Connecter un appareil Windows IoT Core à votre application Azure IoT Central

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Windows IoT Cor à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).

- Un appareil exécutant le système d’exploitation Windows 10 IoT Core. Pour plus d’informations, consultez [la configuration de votre appareil Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Un ordinateur de développement avec [Node.js](https://nodejs.org/) version 8.0.0 ou version ultérieure. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="the-sample-devkits-application"></a>L’application exemple Devkits

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Windows IoT Core** avec les caractéristiques suivantes :

- Mesures de télémétrie de l’appareil : **Humidité**, **température**, et **pression**.
- Paramètre pour indiquer **vitesse du ventilateur**.
- Une propriété d’appareil **meurent numéro** et une propriété cloud **emplacement**.

Pour plus d’informations sur la configuration du modèle d’appareil, consultez [détails du modèle d’appareil Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, utilisez le **Device Explorer** page pour ajouter un appareil réel à partir de la **Windows 10 IoT Core** modèle de périphérique. Prenez note de l’appareil détails de connexion (**ID de portée**, **ID d’appareil**, et **clé primaire**). Pour plus d’informations, consultez [obtenir des informations de connexion](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour l’appareil pour se connecter à IoT Central, il a besoin d’une chaîne de connexion.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Pour le code d’appareil pour accéder à la chaîne de connexion, enregistrez-le dans un fichier appelé **connection.string.iothub** dans le dossier `C:\Data\Users\DefaultAccount\Documents\` sur votre appareil Windows 10 IoT Core.

Pour copier le **connection.string.iothub** fichier à partir de votre ordinateur de bureau pour la `C:\Data\Users\DefaultAccount\Documents\` dossier sur votre appareil, vous pouvez utiliser la [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Utilisez votre navigateur web pour accéder à la Windows Device Portal sur votre appareil.
1. Pour parcourir les fichiers sur votre appareil, choisissez **applications > Explorateur de fichiers**.
1. Accédez à **utilisateur Folders\Documents**. Puis chargez le **connection.string.iothub** fichier :

    ![Télécharger la chaîne de connexion](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Déployer et exécuter

Pour déployer et exécuter l’exemple d’application sur votre appareil, vous pouvez utiliser la [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Utilisez votre navigateur web pour accéder à la Windows Device Portal sur votre appareil.
1. Pour déployer et exécuter le **Azure IoT Hub Client** application, choisissez **applications > exemples de démarrage rapide**. Puis choisissez **Azure IoT Hub Client**.
1. Puis choisissez **déployer et exécuter**.

    ![Déployer et exécuter](media/howto-connect-windowsiotcore/quick-run.png)

Après quelques minutes, vous pouvez afficher les données de télémétrie à partir de votre appareil dans votre application IoT Central.

Le [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) inclut des outils que vous pouvez utiliser pour résoudre les problèmes de votre appareil :

- Le **Gestionnaire d’applications** page vous permet de contrôler les applications en cours d’exécution sur votre appareil.
- Si vous n’avez pas un moniteur connectés à votre appareil, vous pouvez utiliser la **paramètres du périphérique** page pour créer les captures d’écran à partir de votre appareil. Par exemple : 

    ![Capture d’écran de l’application](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Télécharger le code source

Si vous souhaitez Explorer et modifier le code source pour l’application cliente, vous pouvez le télécharger à partir de la [référentiel GitHub d’exemples-iotcore Windows](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Détails de modèle d’appareil

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Windows IoT Core** avec les caractéristiques suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

| Nom du champ     | Units  | Minimale | Maximale | Nombre de décimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humidité       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pression       | hPa    | 260     | 1 260    | 0              |

### <a name="settings"></a>Paramètres

Paramètres numériques

| Nom complet | Nom du champ | Units | Nombre de décimales | Minimale | Maximale | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Vitesse du ventilateur    | fanSpeed   | TR/MIN   | 0              | 0       | 1 000    | 0       |

### <a name="properties"></a>properties

| Type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | number    |
| Texte            | Lieu     | location   | S.O.       |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un Raspberry Pi à votre application Azure IoT Central, l’étape suivante suggérée consiste à apprendre comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.