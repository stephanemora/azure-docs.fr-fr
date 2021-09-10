---
title: Utiliser votre smartphone comme appareil Azure IoT
description: Guide pratique qui vous montre comment transformer votre smartphone en appareil IoT à l’aide de l’application Azure IoT Plug-and-Play.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: how-to
ms.date: 05/27/2021
ms.author: dobett
ms.openlocfilehash: 0daffdab23d5da069598512f1934cfdc04bf01b3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112305894"
---
# <a name="how-to-turn-your-smartphone-into-an-iot-device"></a>Comment transformer votre smartphone en appareil IoT

Une solution Azure IoT vous permet de connecter vos appareils IoT à un service IoT informatique. Les appareils envoient des données de télémétrie, telles que la température et l’humidité, et répondent à des commandes telles que le redémarrage et la modification de l’intervalle de livraison. Les appareils peuvent également synchroniser leur état interne avec le service, en partageant des propriétés telles que le modèle de l’appareil et le système d’exploitation.

L’application téléphonique IoT Plug-and-Play vous permet de commencer rapidement à explorer les capacités d’Azure IoT sans avoir à configurer un appareil IoT dédié.

## <a name="azure-iot-plug-and-play-app"></a>Application Azure IoT Plug-and-Play

Pour vous aider à démarrer rapidement, cet article utilise une application pour smartphone comme appareil IoT. L’application envoie les données de télémétrie collectées à partir des capteurs du téléphone, répond aux commandes appelées à partir du service et rapporte les valeurs des propriétés.

Vous pouvez utiliser cette application smartphone :

- Pour explorer un scénario IoT de base.
- Pour gérer et interagir avec votre téléphone à distance.
- Pour tester votre configuration.
- Comme point de départ pour le développement de votre appareil personnalisé.

## <a name="install-the-app"></a>Installer l’application

[!INCLUDE [iot-phoneapp-install](../../includes/iot-phoneapp-install.md)]

## <a name="app-features"></a>Fonctionnalités de l’application

### <a name="connect"></a>Se connecter

Vous pouvez vous connecter à une application IoT Central en scannant un code QR dans IoT Central.

Pour en savoir plus, consultez la section [Connecter l’application](#connect-the-app) plus loin dans ce guide.

### <a name="telemetry"></a>Télémétrie

L’application collecte les données des capteurs du téléphone pour les envoyer sous forme de télémétrie au service IoT que vous utilisez. Par défaut, les données des capteurs sont agrégées toutes les cinq secondes, mais vous pouvez modifier cette fréquence sur la page des paramètres de l’application :

:::image type="content" source="media/iot-phone-app-how-to/telemetry.png" alt-text="Capture d’écran de la page de télémétrie dans l’application pour smartphone.":::

La capture d’écran suivante montre une vue de l’appareil dans IoT Central qui affiche une partie de la télémétrie de l’appareil :

:::image type="content" source="media/iot-phone-app-how-to/central-telemetry.png" alt-text="Capture d’écran de la télémétrie de l’appareil dans IoT Central.":::

### <a name="properties"></a>Propriétés

L’application indique l’état de l’appareil, comme le modèle et le fabricant de l’appareil. Vous pouvez également modifier la propriété altérable et voir la modification se synchroniser dans votre solution AzureIoT :

:::image type="content" source="media/iot-phone-app-how-to/properties.png" alt-text="Capture d’écran qui montre la page des propriétés dans l’application pour appareils mobiles.":::

La capture d’écran suivante montre la propriété accessible en écriture dans IoT Central après l’envoi de la propriété à l’appareil :

:::image type="content" source="media/iot-phone-app-how-to/central-writable-property.png" alt-text="Capture d’écran montrant la propriété accessible en écriture dans IoT Central.":::

### <a name="image-upload"></a>Chargement d’images

IoT Central et IoT Hub permettent tous deux le chargement de fichiers vers le stockage Azure depuis un appareil. L’application pour smartphone vous permet de charger une image à partir de l’appareil.

Pour en savoir plus sur la configuration de votre service afin de prendre en charge les chargements de fichiers depuis un appareil, consultez :

- [Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub](../iot-hub/iot-hub-csharp-csharp-file-upload.md).
- [Charger des fichiers sur le cloud à partir d’un appareil avec IoT Central](../iot-central/core/howto-configure-file-uploads.md).

:::image type="content" source="media/iot-phone-app-how-to/image-upload.png" alt-text="Capture d’écran qui montre la page de chargement d’images dans l’application pour smartphone.":::

### <a name="logs"></a>Journaux d’activité

L’application pour smartphone écrit les événements dans un fichier journal local que vous pouvez consulter à partir de l’application. Utilisez le fichier journal pour résoudre les problèmes et mieux comprendre ce que fait l’application :

:::image type="content" source="media/iot-phone-app-how-to/logs.png" alt-text="Capture d’écran qui montre la page de journal dans l’application pour smartphone.":::

### <a name="settings"></a>Paramètres

La page des paramètres de l’application vous permet d’effectuer les opérations suivantes :

- Connecter l’application à votre solution Azure IoT.
- Consulter les informations d’inscription de l’appareil.
- Réinitialiser l’application en effaçant les données stockées.
- Personnaliser l’apparence de l’application.
- Définir la fréquence à laquelle l’application envoie la télémétrie à votre service IoT.

:::image type="content" source="media/iot-phone-app-how-to/settings.png" alt-text="Capture d’écran de la page des paramètres de l’application pour smartphone.":::

## <a name="connect-the-app"></a>Connecter l’application

### <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

<!-- To do: does this need an app template? -->
Créez une application IoT Central. Pour plus d’informations, consultez [Créer une application IoT Central](../iot-central/core/howto-create-iot-central-application.md).

### <a name="register-a-device"></a>Inscrire un appareil

Avant de connecter l’application téléphonique, vous devez inscrire un appareil dans votre application IoT Central. Lorsque vous créez une inscription d’appareil, IoT Central génère les informations de connexion de l’appareil.

Pour inscrire l’appareil dans IoT Central :

1. Connectez-vous à votre application IoT Central et accédez à la page **Appareils**.

1. Sélectionnez **Créer un appareil**.

1. Sur la page **Créer un appareil**, sélectionnez **Créer** :

    :::image type="content" source="media/iot-phone-app-how-to/iot-central-create-device.png" alt-text="Capture d’écran montrant comment créer un appareil dans IoT Central.":::

1. Dans la liste des appareils, cliquez sur le nom de l’appareil, puis sélectionnez **Connecter**. Sur la page **Connexion de l’appareil**, vous pouvez voir le code QR que vous allez scanner dans l’application pour smartphone :

    :::image type="content" source="media/iot-phone-app-how-to/device-connection-qr-code.png" alt-text="Capture d’écran montrant la page de connexion de l’appareil avec le code QR.":::

### <a name="connect-the-device"></a>Connecter l’appareil

Une fois l’appareil inscrit dans IoT Central, vous pouvez connecter l’application pour smartphone en scannant le code QR. Pour connecter l’application :

1. Ouvrez l’application **IoT PNP** sur votre smartphone.

1. Sur la page d'accueil, sélectionnez **Scanner le code QR**. Pointez l’appareil photo du téléphone vers le code QR. Après quelques minutes, la connexion est établie.

1. Dans la page des données de télémétrie de l’application, vous pouvez voir les données que l’application envoie à IoT Central. Dans la page des journaux, vous pouvez voir l’appareil qui se connecte et plusieurs messages d’initialisation.

1. Sur la page **Paramètres > Inscription**, vous pouvez voir l’identité d’appareil et l’étendue de l’identifiant que l’application a utilisé pour se connecter à IoT Central.

Pour en savoir plus sur la façon dont les appareils se connectent à IoT Central, consultez [Se connecter à Azure IoT Central](../iot-central/core/concepts-get-connected.md).

### <a name="verify-the-connection"></a>Vérifier la connexion

Pour afficher les données que l’appareil envoie dans votre application IoT Central :

1. Connectez-vous à votre application IoT Central et accédez à la page **Appareils**. Le modèle d’appareil **Smartphone** a automatiquement été attribué à l’appareil.

    > [!TIP]
    > Vous devrez peut-être actualiser la page dans votre navigateur web pour voir que le modèle d’appareil **Smartphone** a été attribué à l’appareil.

1. Dans la liste des appareils, cliquez sur le nom de l’appareil, puis sélectionnez **Vue d’ensemble**. La page **Vue d’ensemble** montre la télémétrie des capteurs du smartphone :

    :::image type="content" source="media/iot-phone-app-how-to/smartphone-overview.png" alt-text="Capture d’écran de la page Vue d’ensemble de l’appareil dans IoT Central qui montre la télémétrie des capteurs du smartphone.":::

1. Consultez la page **À propos de** pour voir les propriétés envoyées par l’appareil.

1. Sur la page **Commandes**, exécutez la commande **LightOn** pour allumer le flash du téléphone.

> [!TIP]
> La page **Données brutes** affiche toutes les données provenant de l’appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez connecté votre application pour smartphone à IoT Central, la prochaine étape consiste à en savoir plus sur [IoT Central](../iot-central/core/overview-iot-central.md).
