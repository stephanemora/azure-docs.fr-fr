---
title: Connecter un appareil DevKit à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central en utilisant IoT Plug-and-Play.
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 32dd3fa1fc137d786174e47d842f762c2a479d64
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848952"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application-preview-features"></a>Connecter un appareil DevKit IoT MXChip à une application Azure IoT Central (fonctionnalités en préversion)

Cet article vous montre comment connecter un appareil MXChip IoT DevKit (DevKit) à une application Azure IoT Central. L'appareil utilise le modèle certifié IoT Plug and Play pour l'appareil DevKit afin de configurer sa connexion à IoT Central.

Dans cet article sur les procédures, vous :

- Obtenez les détails de connexion depuis votre application IoT Central ,
- Préparez l’appareil et le connectez à votre application IoT Central ;
- Affichez les données de télémétrie et les propriétés de l'appareil dans IoT Central.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des ressources suivantes :

- Un [appareil DevKit](https://aka.ms/iot-devkit-purchase).
- Une application IoT Central créée à partir du modèle d’**application en préversion**. Vous pouvez suivre les étapes de la section [Créer une application IoT Plug-and-Play](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Obtenir des informations relatives à la connexion de l’appareil

1. Dans votre application Azure IoT Central, sélectionnez l’onglet **Modèles d’appareil**, puis sélectionnez **Nouveau**. Dans la section **Utiliser un modèle d’appareil préconfiguré**, choisissez **MXChip IoT DevKit** dans la liste. Puis, sélectionnez **Suivant : Personnaliser** et **Créer**.

    ![Modèle d’appareil pour MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Sélectionnez l’onglet **Appareils** dans la liste d’appareils, sélectionnez **MXChip IoT DevKit**, puis **Nouveau** pour créer un nouvel appareil à partir d’un modèle d’appareil.

    ![Nouvel appareil](media/howto-connect-devkit/new-device.png)

1. Dans la fenêtre contextuelle, entrez **l’ID de l’appareil** en tant que `SampleDevKit` et le**nom de l’appareil** en tant que `MXChip IoT DevKit - Sample`. Assurez-vous d’avoir désactiver l’option **Simulé**. Sélectionnez ensuite **Créer**.

    ![ID et nom de l’appareil](media/howto-connect-devkit/device-id-name.png)

1. Cliquez sur l’appareil que vous venez de créer, puis sélectionnez **Connecter**. Notez **l’étendue de l’ID**, **l’ID de l’appareil** et la **Clé primaire**.

    ![Informations sur la connexion de l’appareil](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Préparer l’appareil

1. Téléchargez la dernière version du [microprogramme Azure IoT Central Plug and Play prédéfini](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) pour l’appareil DevKit à partir de GitHub.

1. Connectez l’appareil DevKit à votre machine de développement à l’aide d’un câble USB. Dans Windows, une fenêtre Explorateur de fichiers s’ouvre sur un lecteur mappé au stockage de l’appareil DevKit. Par exemple, le lecteur peut s’appeler **AZ3166 (d)** .

1. Faites glisser le fichier **iotc_devkit.bin** jusqu’à la fenêtre du lecteur. Une fois la copie effectuée, l’appareil redémarre avec le nouveau microprogramme.

    > [!NOTE]
    > Si vous voyez des erreurs sur l'écran telles que **No Wi-Fi** (Pas de Wi-Fi), alors le DevKit n'a pas encore été connecté au réseau Wi-Fi.

1. Dans DevKit, maintenez le **bouton B** enfoncé, appuyez brièvement sur le bouton **Réinitialiser**, puis relâchez le **bouton B**. L’appareil est maintenant en mode point d’accès. Pour confirmer, l'écran affiche « IoT DevKit - AP » et l'adresse IP du portail de configuration.

1. Sur votre ordinateur ou tablette, connectez-vous au réseau Wi-Fi dont le nom est affiché sur l’écran de l’appareil. Le réseau Wi-Fi commence par **AZ-** suivi de l'adresse MAC. Quand vous vous connectez à ce réseau, vous n’avez pas accès à Internet. Il s’agit d’un état normal. Vous êtes connecté uniquement à ce réseau pendant un bref laps de temps, le temps de configurer l’appareil.

1. Ouvrez votre navigateur web et accédez à [http://192.168.0.1/](http://192.168.0.1/). La page web suivante s’affiche à l’écran :

    ![Config UI](media/howto-connect-devkit/config-ui.png)

    Dans la page web, entrez :

    - Le nom de votre réseau Wi-Fi (SSID).
    - Le mot de passe de votre réseau Wi-Fi.
    - Informations relatives à la connexion : entrez **l’ID de l’appareil**, **l’étendue de l’ID** et la **clé primaire SAS** dont vous avez pris note précédemment.

    > [!NOTE]
    > Actuellement, l’IoT DevKit ne peut se connecter qu'à des réseaux Wi-Fi 2,4 GHz ; les réseaux 5 GHz ne sont pas pris en charge en raison de restrictions matérielles.

1. Choisissez **Configurer l’appareil**. L’appareil DevKit redémarre et exécute l'application :

    ![Reboot UI](media/howto-connect-devkit/reboot-ui.png)

    L’écran DevKit affiche un message confirmant l’exécution de l’application :

    ![DevKit running](media/howto-connect-devkit/devkit-running.png)

Le DevKit enregistre d'abord un nouvel appareil dans l'application IoT Central et commence ensuite à envoyer des données.

## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

Dans cette étape, vous visualisez les données de télémétrie dans votre application Azure IoT Central.

Dans votre application IoT Central, sélectionnez l’onglet **Appareils**, puis choisissez l’appareil que vous avez ajouté. L’onglet **Vue d’ensemble** affiche les données de télémétrie de l’appareil DevKit :

![Vue d’ensemble de l’appareil IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Vérifier le code

Pour examiner le code ou le modifier et le compiler, accédez à des [exemples de code](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un appareil DevKit à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](./howto-set-up-template.md) pour votre propre appareil IoT.
