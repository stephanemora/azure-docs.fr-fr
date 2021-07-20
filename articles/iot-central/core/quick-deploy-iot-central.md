---
title: Démarrage rapide - Créer et utiliser une application Azure IoT Central | Microsoft Docs
description: Démarrage rapide - Créer une application Azure IoT Central et connecter votre premier appareil. Ce démarrage rapide utilise une application de smartphone à partir du Google Play ou d’Apple App Store en tant qu’appareil IoT.
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 4e40c079c50ccb7f83a4e03d47cf3e9b419870ef
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288486"
---
# <a name="quickstart---create-an-azure-iot-central-application-and-use-your-smartphone-to-send-telemetry"></a>Démarrage rapide : Créer une application Azure IoT Central et utiliser votre smartphone pour envoyer des données de télémétrie

Ce démarrage rapide vous montre comment créer une application Azure IoT Central et connecter votre premier appareil. Pour démarrer rapidement, vous installez une application sur votre smartphone pour faire office d’appareil. L’application envoie des données de télémétrie, des propriétés de rapports et répond à des commandes :

:::image type="content" source="media/quick-deploy-iot-central/overview.png" alt-text="Vue d’ensemble du scénario de démarrage rapide de connexion d’une application de smartphone à IoT Central." border="false":::

## <a name="prerequisites"></a>Prérequis

Un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!TIP]
> Vous devez avoir au moins un accès **Contributeur** dans votre abonnement Azure. Si vous avez créé l’abonnement vous-même, vous êtes automatiquement un administrateur disposant d’un accès suffisant. Pour en savoir plus, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure ?](../../role-based-access-control/overview.md)

Un téléphone Android ou iOS sur lequel vous pouvez installer une application gratuite à partir de l’un des magasins d’applications officiels.

## <a name="create-an-application"></a>Créer une application

Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Connectez-vous ensuite avec le compte Microsoft personnel, professionnel ou scolaire associé à votre abonnement Azure.

IoT Central fournit différents modèles d’application centrés sur le secteur pour vous aider à bien démarrer. Ce démarrage rapide utilise le modèle **Application personnalisée** pour créer une application à partir de zéro :

1. Accédez à la page **Build**, puis sélectionnez **Créer une application** dans la mosaïque **Application personnalisée** :

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Page Générer votre application IoT":::

1. Dans la page **Nouvelle application**, vérifiez que l’option **Application personnalisée** est sélectionnée sous le **Modèle d’application**.

1. Azure IoT Central suggère automatiquement un **Nom d’application** basé sur le modèle d’application que vous avez sélectionné. Entrez votre propre nom d’application, par exemple *Application de démarrage rapide Contoso*.

1. Azure IoT Central génère aussi pour vous un préfixe d’**URL** unique basé sur le nom de l’application. Vous utiliserez cette URL pour accéder à votre application. Si vous le souhaitez, remplacez ce préfixe d’URL par une chaîne plus facile à mémoriser. Cette URL doit être unique.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central - Page Création d’une application":::

1. Pour ce démarrage rapide, laissez le plan de tarification défini sur **Standard 2**.

1. Sélectionnez votre abonnement dans la liste déroulante **Abonnement Azure**.

1. Sélectionnez votre emplacement le plus proche dans la liste déroulante **Emplacement**.

1. Consultez les Conditions d’utilisation et sélectionnez **Créer** en bas de la page. Après quelques secondes, votre application IoT Central est prête à être utilisée :

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Application Azure IoT Central":::

## <a name="register-a-device"></a>Inscrire un appareil

Pour connecter un appareil à votre application IoT Central, vous avez besoin de certaines informations de connexion. Pour facilement obtenir ces informations de connexion, inscrivez votre appareil.

Pour inscrire votre appareil :

1. Dans IoT Central, accédez à la page **Appareils** , puis sélectionnez **Créer un appareil** :

    :::image type="content" source="media/quick-deploy-iot-central/create-device.png" alt-text="Capture d’écran montrant comment créer un appareil dans IoT Central.":::

1. Dans la page **Créer un appareil**, acceptez les valeurs par défaut, puis sélectionnez **Créer**.

1. Dans la liste des appareils, cliquez sur le nom de l’appareil :

    :::image type="content" source="media/quick-deploy-iot-central/device-name.png" alt-text="Capture d’écran montrant le nom de l’appareil en surbrillance que vous pouvez sélectionner.":::

1. Dans la page de l’appareil, sélectionnez **Connecter** puis **Code QR** :

    :::image type="content" source="media/quick-deploy-iot-central/device-registration.png" alt-text="Capture d’écran montrant le code QR que vous pouvez utiliser pour connecter l’application de téléphone.":::

Ne fermez pas cette page. Dans la section suivante, scannez ce code QR à l’aide de l’application du téléphone pour le connecter à IoT Central.

## <a name="connect-your-device"></a>Connecter votre appareil

Pour vous aider à démarrer rapidement, cet article utilise l’application de smartphone **IoT Plug-and-Play** en tant qu’appareil IOT. L’application envoie les données de télémétrie collectées à partir des capteurs du téléphone, répond aux commandes appelées à partir d’IoT Central et signale les valeurs de propriété à IoT Central.

[!INCLUDE [iot-phoneapp-install](../../../includes/iot-phoneapp-install.md)]

Pour connecter l’application **IoT Plug-and-Play** à votre application IOT central :

1. Ouvrez l’application **IoT PNP** sur votre smartphone.

1. Sur la page d'accueil, sélectionnez **Scanner le code QR**. Pointez l’appareil photo du téléphone vers le code QR. Après quelques minutes, la connexion est établie.

1. Dans la page des données de télémétrie de l’application, vous pouvez voir les données que l’application envoie à IoT Central. Dans la page des journaux, vous pouvez voir l’appareil qui se connecte et plusieurs messages d’initialisation.

Pour afficher les données de télémétrie à partir de l’application de smartphone dans IoT Central :

1. Dans IoT Central, accédez à la page **Appareils**.

1. Dans la liste des appareils, cliquez sur le nom de votre appareil, puis sélectionnez **Vue d’ensemble** :

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-telemetry.png" alt-text="Capture d’écran de la page Vue d’ensemble avec les tracés de télémétrie.":::

> [!TIP]
> L’application de smartphone envoie uniquement des données lorsque l’écran est activé.
## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé une application IoT Central et un appareil connecté qui envoie des données de télémétrie. Dans ce démarrage rapide, vous avez utilisé une application de smartphone en tant qu’appareil IoT se connectant à IoT Central. Voici l’étape suivante suggérée pour poursuivre votre apprentissage sur IoT Central :

> [!div class="nextstepaction"]
> [Ajouter une règle à votre application IoT Central](./quick-configure-rules.md)
