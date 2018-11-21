---
title: Inscrire un nouvel appareil Azure IoT Edge (VS Code) | Microsoft Docs
description: Utiliser Visual Studio Code pour créer un appareil IoT Edge dans votre hub Azure IoT
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf9603c65454f076a494789e784c9352fb7bef33
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578703"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Inscrire un nouvel appareil Azure IoT Edge à partir de Visual Studio Code

Avant d’utiliser vos appareils IoT avec Azure IoT Edge, vous devez les inscrire auprès de votre hub IoT. Une fois que vous avez inscrit un appareil, vous recevez une chaîne de connexion qui vous permet de le configurer pour les charges de travail Edge. 

Cet article explique comment inscrire un nouvel appareil IoT Edge à l’aide de Visual Studio Code (VS Code). Il existe plusieurs façons d’effectuer la plupart des opérations dans VS Code. Cet article utilise l’Explorateur, mais vous pouvez également utiliser la palette de commandes pour exécuter la plupart des étapes. 

## <a name="prerequisites"></a>Prérequis

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pour Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

Vous pouvez utiliser les extensions Azure IoT pour Visual Studio Code afin d’effectuer des opérations avec votre hub IoT. Pour que ces opérations fonctionnent, vous devez vous connecter à votre compte Azure, puis sélectionner le hub IoT sur lequel vous travaillez.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.

2. En bas de l’Explorateur, développez la section **Appareils Azure IoT Hub**. 

   ![Développer Appareils Azure IoT Hub](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Cliquez sur **...** dans l’en-tête de section **Appareils Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête. 

4. Choisissez **Sélectionner un hub IoT**.

5. Si vous n’êtes pas connecté à votre compte Azure, suivez les invites à cette fin. 

6. Sélectionnez votre abonnement Azure. 

7. Sélectionnez votre hub IoT. 

## <a name="create-a-device"></a>Créer un appareil

1. Dans l’Explorateur VS Code, développez la section **Appareils Azure IoT Hub**. 

2. Cliquez sur **...** dans l’en-tête de section **Appareils Azure IoT Hub**. Si vous ne voyez pas les points de suspension, cliquez ou pointez sur l’en-tête. 

3. Sélectionnez **Créer un appareil IoT Edge**. 

4. Dans la zone de texte qui s’ouvre, donnez un ID à votre appareil. 

Dans l’écran de sortie, vous voyez le résultat de la commande. Les informations de l’appareil apparaissent, notamment l’ID d’appareil (**deviceId**) que vous avez fourni et la chaîne de connexion (**connectionString**) qui vous permet de connecter votre appareil physique à votre hub IoT. 

## <a name="view-all-devices"></a>Voir tous les appareils

Tous les appareils qui se connectent à votre hub IoT sont répertoriés dans la section **Appareils Azure IoT Hub** de l’Explorateur de Visual Studio Code. Les appareils IoT Edge se distinguent des appareils non-Edge par leur icône spécifique et par le fait que vous pouvez les développer pour afficher les modules déployés sur chacun d’eux. 

   ![Voir les appareils dans VS Code](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Récupérer la chaîne de connexion

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Cliquez avec le bouton droit sur l’ID de votre appareil dans la section **Appareils Azure IoT Hub**. 
2. Sélectionnez **Copier la chaîne de connexion de l’appareil**.

   La chaîne de connexion est copiée dans le Presse-papiers. 

Vous pouvez également sélectionner **Obtenir les informations de l’appareil** dans le menu contextuel pour voir les informations de l’appareil, notamment la chaîne de connexion, dans la fenêtre de sortie. 


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer des modules sur un appareil avec Visual Studio Code](how-to-deploy-modules-vscode.md).
