---
title: Inscrire un nouvel appareil à partir de Visual Studio Code - Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour créer un appareil IoT Edge dans votre hub Azure IoT et récupérer la chaîne de connexion
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 01/03/2019
ms.date: 01/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2b851e7f2ebdbff08fa09002765fbd7d7927deba
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126288"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Inscrire un nouvel appareil Azure IoT Edge à partir de Visual Studio Code

Avant d’utiliser vos appareils IoT avec Azure IoT Edge, vous devez les inscrire auprès de votre hub IoT. Une fois que vous avez inscrit un appareil, vous recevez une chaîne de connexion qui vous permet de le configurer pour les charges de travail Edge. 

Cet article explique comment inscrire un nouvel appareil IoT Edge à l’aide de Visual Studio Code (VS Code). Il existe plusieurs façons d’effectuer la plupart des opérations dans VS Code. Cet article utilise l’Explorateur, mais vous pouvez également utiliser la palette de commandes pour exécuter la plupart des étapes. 

## <a name="prerequisites"></a>Conditions préalables

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Se connecter pour accéder à votre hub ioT

Vous pouvez utiliser les extensions Azure IoT pour Visual Studio Code afin d’effectuer des opérations avec votre hub IoT. Pour que ces opérations fonctionnent, vous devez vous connecter à votre compte Azure, puis sélectionner le hub IoT sur lequel vous travaillez.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.

2. En bas de l’Explorateur, développez la section **Appareils Azure IoT Hub**. 

   ![Développer la section Appareils Azure IoT Hub](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

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

   ![Afficher tous les appareils IoT Edge de votre hub IoT](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Récupérer la chaîne de connexion

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Cliquez avec le bouton droit sur l’ID de votre appareil dans la section **Appareils Azure IoT Hub**. 
2. Sélectionnez **Copier la chaîne de connexion de l’appareil**.

   La chaîne de connexion est copiée dans le Presse-papiers. 

Vous pouvez également sélectionner **Obtenir les informations de l’appareil** dans le menu contextuel pour voir les informations de l’appareil, notamment la chaîne de connexion, dans la fenêtre de sortie. 


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer des modules sur un appareil avec Visual Studio Code](how-to-deploy-modules-vscode.md).
