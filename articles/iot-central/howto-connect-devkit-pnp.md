---
title: Connecter un appareil DevKit à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central en utilisant IoT Plug-and-Play.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: b7d2e1b08653cb8023ef6a5190ab53ecc3d568a6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297050"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central

Cet article vous montre comment connecter un appareil MXChip IoT DevKit (DevKit) à une application Azure IoT Central. L'appareil utilise le modèle certifié IoT Plug and Play pour l'appareil DevKit afin de configurer sa connexion à IoT Central.

Dans cet article sur les procédures, vous :

- Obtenez les détails de connexion depuis votre application IoT Central ,
- Préparez l’appareil et le connectez à votre application IoT Central ;
- Affichez les données de télémétrie et les propriétés de l'appareil dans IoT Central.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des ressources suivantes :

1. Un [appareil DevKit](https://aka.ms/iot-devkit-purchase).
1. Une application IoT Central créée à partir du modèle d’**application en préversion**. Vous pouvez suivre les étapes de la section [Créer une application IoT Plug-and-Play](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="get-device-connection-details"></a>Obtenir des informations relatives à la connexion de l’appareil

Dans votre application Azure IoT Central, sélectionnez l'onglet **Administration**, puis choisissez **Connexion de l’appareil**. Prenez note des valeurs **Étendue de l’ID** et **Clé primaire**.

![Informations relatives à la connexion du groupe d’appareils](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>Préparer l’appareil

1. Téléchargez la dernière version du [microprogramme Azure IoT Central Plug and Play prédéfini](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) pour l’appareil DevKit à partir de GitHub.

1. Connectez l’appareil DevKit à votre machine de développement à l’aide d’un câble USB. Dans Windows, une fenêtre Explorateur de fichiers s’ouvre sur un lecteur mappé au stockage de l’appareil DevKit. Par exemple, le lecteur peut s’appeler **AZ3166 (d)** .

1. Faites glisser le fichier **iotc_devkit.bin** jusqu’à la fenêtre du lecteur. Une fois la copie effectuée, l’appareil redémarre avec le nouveau microprogramme.

    > [!NOTE]
    > Si vous voyez des erreurs sur l'écran telles que **No Wi-Fi** (Pas de Wi-Fi), alors le DevKit n'a pas encore été connecté au réseau Wi-Fi.

1. Dans DevKit, maintenez le **bouton B** enfoncé, appuyez brièvement sur le bouton **Réinitialiser**, puis relâchez le **bouton B**. L’appareil est maintenant en mode point d’accès. Pour confirmer, l'écran affiche « IoT DevKit - AP » et l'adresse IP du portail de configuration.

1. Sur votre ordinateur ou tablette, connectez-vous au réseau Wi-Fi dont le nom est affiché sur l’écran de l’appareil. Le réseau Wi-Fi commence par **AZ-** suivi de l'adresse MAC. Quand vous vous connectez à ce réseau, vous n’avez pas accès à Internet. Il s’agit d’un état normal. Vous êtes connecté uniquement à ce réseau pendant un bref laps de temps, le temps de configurer l’appareil.

1. Ouvrez votre navigateur web et accédez à [http://192.168.0.1/](http://192.168.0.1/). La page web suivante s’affiche à l’écran :

    ![Config UI](media/howto-connect-devkit-pnp/config-ui.png)

    Dans la page web, entrez :

    - Le nom de votre réseau Wi-Fi (SSID).
    - Le mot de passe de votre réseau Wi-Fi.
    - Les informations relatives à la connexion : l’**ID de l’appareil** que vous pouvez choisir vous-même, ainsi que l’**étendue de l’ID** et la **clé primaire SAS de groupe** dont vous avez pris note précédemment.

    > [!NOTE]
    > Actuellement, l’IoT DevKit ne peut se connecter qu'à des réseaux Wi-Fi 2,4 GHz ; les réseaux 5 GHz ne sont pas pris en charge en raison de restrictions matérielles.

1. Choisissez **Configurer l’appareil**. L’appareil DevKit redémarre et exécute l'application :

    ![Reboot UI](media/howto-connect-devkit-pnp/reboot-ui.png)

    L’écran DevKit affiche un message confirmant l’exécution de l’application :

    ![DevKit running](media/howto-connect-devkit-pnp/devkit-running.png)

Le DevKit enregistre d'abord un nouvel appareil dans l'application IoT Central et commence ensuite à envoyer des données.

## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

Dans cette étape, vous visualisez les données de télémétrie dans votre application Azure IoT Central.

Dans votre application IoT Central, sélectionnez l’onglet **Appareils**, puis choisissez l’appareil que vous avez ajouté. L’onglet **Vue d’ensemble** affiche les données de télémétrie de l’appareil DevKit :

   ![Vue d’ensemble de l’appareil IoT Central](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>Vérifier le code

Pour examiner le code ou le modifier et le compiler, accédez à des [exemples de code](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un appareil DevKit à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) pour votre propre appareil IoT.
