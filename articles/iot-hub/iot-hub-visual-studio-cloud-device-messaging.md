---
title: Gérer les messages cloud-à-appareil Azure IoT Hub avec Cloud Explorer pour Visual Studio | Microsoft Docs
description: Découvrez comment utiliser Cloud Explorer pour Visual Studio afin de superviser les messages appareil-à-cloud et d’envoyer des messages cloud-à-appareil dans Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571309"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Utiliser Cloud Explorer pour Visual Studio afin d’envoyer et de recevoir des messages entre votre appareil et un hub IoT

![Diagramme de bout en bout](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) est une extension Visual Studio utile qui vous permet de visualiser vos ressources Azure, d’inspecter leurs propriétés et d’exécuter des actions de développeur essentielles à partir de Visual Studio. Cet article se concentre sur l’utilisation de Cloud Explorer pour l’envoi et la réception de messages entre votre appareil et votre hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Contenu

Vous allez découvrir comment utiliser Cloud Explorer pour Visual Studio afin de superviser les messages appareil-à-cloud et d’envoyer des messages cloud-à-appareil. Les messages appareil-à-cloud peuvent être des données de capteur collectées par votre appareil, puis envoyées vers votre hub IoT. Les messages cloud-à-appareil peuvent être des commandes envoyées à votre appareil par votre hub IoT. Par exemple, faire clignoter une LED qui est connectée à votre appareil.

## <a name="what-you-will-do"></a>Procédure à suivre

- Utiliser Cloud Explorer pour Visual Studio afin de superviser les messages appareil-à-cloud.
- Utiliser Cloud Explorer pour Visual Studio pour envoyer des messages cloud-à-appareil.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Un abonnement Azure actif.
- Un hub IoT Azure associé à votre abonnement
- Microsoft Visual Studio 2017 Update 8 ou ultérieur
- Composant Cloud Explorer de Visual Studio Installer (sélectionné par défaut avec la charge de travail Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Mettre à jour Cloud Explorer vers la version la plus récente

Le composant Cloud Explorer de Visual Studio Installer prend uniquement en charge le suivi des messages appareil-à-cloud et cloud-à-appareil. Pour envoyer des messages à l’appareil ou au cloud, vous devez télécharger et installer la dernière version de [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

1. Dans la fenêtre **Cloud Explorer** de Visual Studio, cliquez sur l’icône Gestion de compte. Vous pouvez ouvrir la fenêtre Cloud Explorer à partir du menu **Affichage** > **Cloud Explorer**.

    ![Cliquez sur Gestion de compte.](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Cliquez sur **Gérer les comptes** dans Cloud Explorer.

3. Cliquez sur **Ajouter un compte** dans la nouvelle fenêtre pour vous connecter à Azure pour la première fois.

4. Une fois que vous êtes connecté, la liste de vos abonnements Azure s’affiche. Sélectionnez les abonnements Azure que vous souhaitez visualiser, puis cliquez sur **Appliquer**.

5. Développez **Votre abonnement** > **IoT Hubs** > **Votre IoT Hub**, la liste des appareils s’affichent sous le nœud de votre hub IoT.

    ![Liste des appareils](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Analyse de messages appareil vers cloud

Pour superviser les messages envoyés de votre appareil à votre hub IoT, effectuez les étapes suivantes :

1. Cliquez avec le bouton droit sur votre hub IoT ou votre appareil et sélectionnez **Démarrer la supervision des messages appareil-à-cloud**.

    ![Démarrer la supervision des messages appareil-à-cloud](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. Les messages supervisés sont affichés dans le volet de sortie **IoT Hub**.

    ![Résultat de supervision des messages appareil-à-cloud](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. Pour arrêter la supervision, cliquez avec le bouton droit sur n’importe quel appareil ou hub IoT et sélectionnez **Arrêter la supervision des messages appareil-à-cloud**.

## <a name="send-cloud-to-device-messages"></a>Envoi de messages cloud vers appareil

Pour envoyer un message de votre hub IoT vers votre appareil, effectuez les étapes suivantes :

1. Cliquez avec le bouton droit sur votre appareil et sélectionnez **Envoyer un message cloud-à-appareil**.

    ![Envoyer un message cloud-à-appareil](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. Entrez le message dans la zone de saisie.

3. Les résultats sont affichés dans le volet de sortie **IoT Hub**.

    ![Résultat d’envoi de message cloud-à-appareil](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à analyser des messages appareil vers cloud et à envoyer des messages cloud vers appareil entre votre appareil IoT et l’instance IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]