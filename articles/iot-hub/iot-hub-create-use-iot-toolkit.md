---
title: Créer un hub Azure IoT avec Azure IoT Toolkit pour VS Code | Microsoft Docs
description: Comment utiliser Azure IoT Toolkit pour VS Code pour créer un hub IoT.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: af31f9375d6a41e13a9122e9730ba9532d3d52c5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003067"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Créer un hub Azure IoT avec Azure IoT Toolkit pour Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Vous pouvez utiliser [Azure IoT Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) pour créer des hubs Azure IoT. Cet article vous montre comment créer un hub IoT avec Azure IoT Toolkit.

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Un compte Azure actif.
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

Maintenant que vous avez déployé un hub IoT avec Azure IoT Toolkit pour Visual Studio Code, vous souhaiterez peut-être aller plus loin :

* [Utiliser l’extension Azure IoT Toolkit pour Visual Studio Code afin d’envoyer et de recevoir des messages entre votre appareil et IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).
* [Utiliser l’extension Azure IoT Toolkit pour Visual Studio Code pour la gestion des appareils Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)
* [Page Wiki](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki) pour Azure IoT Toolkit.
