---
title: Créer un hub Azure IoT avec Azure IoT Toolkit pour VS Code | Microsoft Docs
description: Comment utiliser Azure IoT Toolkit pour VS Code pour créer un hub IoT.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 5097d7e1f6a0b9e94919ccc8731702206c0a1568
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047231"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Créer un hub Azure IoT avec Azure IoT Toolkit pour Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Cet article vous montre comment utiliser [Azure ioT Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) afin de créer un hub Azure IoT. 

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>Créer un hub IoT

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.

2. En bas de l’Explorateur, développez la section **Appareils Azure IoT Hub**. 

   ![Développer Appareils Azure IoT Hub](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Cliquez sur **...** dans l’en-tête de section **Appareils Azure IoT Hub**. Si vous ne voyez pas les points de suspension, pointez sur l’en-tête. 

4. Choisissez **Créer un hub IoT**.

5. Une fenêtre contextuelle s’affiche en bas à droite pour vous permettre de vous connecter à Azure pour la première fois.

6. Sélectionnez un abonnement Azure. 

7. Sélectionnez un groupe de ressources.

8. Sélectionnez un emplacement.

9. Sélectionnez un niveau tarifaire.

10. Entrez un nom global unique pour votre hub IoT.

11. Attendez quelques minutes que le hub IoT soit créé.

## <a name="next-steps"></a>Étapes suivantes

Nous avons maintenant déployé un hub Azure IoT avec IoT Toolkit pour Visual Studio Code. Pour en savoir plus, consultez les articles suivants :

* [Utiliser l’extension Azure IoT Toolkit pour Visual Studio Code afin d’envoyer et de recevoir des messages entre votre appareil et un IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Utiliser l’extension Azure IoT Toolkit pour Visual Studio Code pour la gestion des appareils Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Voir la page wiki d’Azure IoT Toolkit](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
