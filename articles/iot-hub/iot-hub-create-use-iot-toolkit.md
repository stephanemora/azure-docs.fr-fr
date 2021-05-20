---
title: Créer un hub Azure IoT avec Azure IoT Tools pour VS Code | Microsoft Docs
description: Découvrez comment utiliser Azure IoT Tools pour Visual Studio Code afin de créer un hub Azure IoT dans un groupe de ressources.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: e8d6bebffc46b0b158aeb975ae1e32a863b956d2
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109655838"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Créer un hub Azure IoT avec Azure IoT Tools pour Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Cet article montre comment utiliser [Azure IoT Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) afin de créer un hub Azure IoT. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) installé pour Visual Studio Code.


## <a name="create-an-iot-hub-and-device-in-an-iot-project"></a>Créer un hub et un appareil IoT dans un projet IoT

Les étapes suivantes expliquent comment créer un hub IoT et y inscrire un appareil dans le cadre d'un projet IoT dans Visual Studio Code.

Au lieu d'approvisionner une instance d'Azure IoT Hub et un appareil à partir du portail Azure, vous pouvez le faire dans VS Code sans quitter l'environnement de développement. Les étapes présentées dans cette section indiquent la marche à suivre.

1. Dans la nouvelle fenêtre de projet ouverte, cliquez sur `F1` pour ouvrir la palette de commandes, puis entrez et sélectionnez **Azure IoT Device Workbench : Approvisionner les services Azure...** Suivez le guide pas à pas pour finaliser l'approvisionnement de votre instance d'Azure IoT Hub et la création de l'appareil IoT Hub.

    ![Commande d'approvisionnement](media/iot-hub-create-use-iot-toolkit/provision.png)

    > [!NOTE]
    > Si vous n’êtes pas connecté dans Azure. Suivez la notification de la fenêtre contextuelle pour vous connecter.

1. Sélectionnez l’abonnement que vous souhaitez utiliser.

    ![Sélectionner l'abonnement](media/iot-hub-create-use-iot-toolkit/select-subscription.png)

1. Sélectionnez un groupe de ressources existant ou créez un nouveau [groupe de ressources](../azure-resource-manager/management/overview.md#terminology).

    ![Sélectionner un groupe de ressources](media/iot-hub-create-use-iot-toolkit/select-resource-group.png)

1. Dans le groupe de ressources que vous avez spécifié, suivez les invites pour sélectionner une instance d'Azure IoT Hub existante ou en créer une nouvelle.

    ![Étapes de sélection d'IoT Hub](media/iot-hub-create-use-iot-toolkit/iot-hub-provision.png)

    ![Sélectionner IoT Hub](media/iot-hub-create-use-iot-toolkit/select-iot-hub.png)

    ![IoT Hub sélectionné](media/iot-hub-create-use-iot-toolkit/iot-hub-selected.png)

1. Dans la fenêtre de sortie, vous verrez l'instance d'Azure IoT Hub approvisionnée.

    ![Instance d'IoT Hub approvisionnée](media/iot-hub-create-use-iot-toolkit/iot-hub-provisioned.png)

1. Sélectionnez ou créez un nouvel appareil IoT Hub dans l'instance d'Azure IoT Hub que vous avez approvisionnée.

    ![Étapes de sélection d'un appareil IoT](media/iot-hub-create-use-iot-toolkit/iot-device-provision.png)

    ![Sélectionner un appareil IoT provisionné](media/iot-hub-create-use-iot-toolkit/select-iot-device.png)

1. Vous disposez maintenant d’une instance provisionnée d’Azure IoT Hub sur laquelle un appareil a été créé. De même, la chaîne de connexion d'appareil est enregistrée dans VS Code.

    ![Approvisionnement terminé](media/iot-hub-create-use-iot-toolkit/provision-done.png)



## <a name="create-an-iot-hub-without-an-iot-project"></a>Créer un hub IoT sans projet IoT

Les étapes suivantes expliquent comment créer un hub IoT sans projet IoT dans Visual Studio Code.

1. Dans Visual Studio Code, ouvrez la vue **Explorateur**.

2. Au bas de l’Explorateur, développez la section **Azure IoT Hub**. 

   ![Développer Appareils Azure IoT Hub](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Cliquez sur **...** dans l’en-tête de section **Azure IoT Hub**. Si vous ne voyez pas les points de suspension, pointez sur l’en-tête. 

4. Choisissez **Créer un hub IoT**.

5. Une fenêtre contextuelle s'affiche en bas à droite afin de vous permettre de vous connecter à Azure pour la première fois.

6. Sélectionnez un abonnement Azure. 

7. Sélectionnez un groupe de ressources.

8. Sélectionnez un emplacement.

9. Sélectionnez un niveau tarifaire.

10. Entrez un nom global unique pour votre hub IoT.

11. Attendez quelques minutes que le hub IoT soit créé.

## <a name="next-steps"></a>Étapes suivantes

Nous avons maintenant déployé un hub Azure IoT avec Azure IoT Tools pour Visual Studio Code. Pour en savoir plus, consultez les articles suivants :

* [Utiliser Azure IoT Tools pour Visual Studio Code afin d’envoyer et de recevoir des messages entre votre appareil et un hub IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Utiliser Azure IoT Tools pour Visual Studio Code pour la gestion des appareils Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Consultez la page wiki Azure IoT Hub pour VS Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
