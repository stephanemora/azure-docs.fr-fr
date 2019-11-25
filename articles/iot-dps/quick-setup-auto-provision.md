---
title: 'Démarrage rapide : Configurer l’approvisionnement de votre appareil dans le portail Azure'
description: 'Démarrage rapide d’Azure : Configurer le service d’approvisionnement d’appareil Azure IoT Hub dans le portail Azure'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 504e027095d839efcbfb535c0e1ecc8c6cfbad26
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903448"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Démarrage rapide : Configurer le service d’approvisionnement d’appareil IoT Hub avec le portail Azure

Ces étapes indiquent comment configurer les ressources cloud Azure dans le portail pour l’approvisionnement de vos appareils. Cet article inclut les étapes à suivre pour la création de votre IoT hub et d’un service IoT Hub Device Provisioning, ainsi que pour la liaison des deux services. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Créer une instance du service d’approvisionnement d’appareil IoT Hub

1. Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2. *Faites des recherches sur la Place de marché* concernant le **service d’approvisionnement d’appareil**. Sélectionnez le **Service IoT Hub Device Provisioning** et cliquez sur le bouton **Créer**. 

3. Fournissez les informations ci-après pour votre nouvelle instance de service Device Provisioning, puis cliquez sur **Créer**.

    * **Nom :** Spécifiez un nom unique pour votre nouvelle instance du service Device Provisioning. Si le nom saisi est disponible, une coche verte s’affiche.
    * **Abonnement :** Choisissez l’abonnement que vous voulez utiliser pour créer cette instance du service Device Provisioning.
    * **Groupe de ressources :** Ce champ vous permet de créer un groupe de ressources ou de sélectionner un groupe déjà existant pour contenir la nouvelle instance. Choisissez le même groupe de ressources qui contient l’IoT Hub que vous avez créé ci-dessus, par exemple, **TestResources**. En plaçant toutes les ressources associées dans un même groupe, vous pouvez les gérer toutes ensemble. Par exemple, si vous supprimez le groupe de ressources, vous supprimez également toutes les ressources contenues dans ce groupe. Pour plus d’informations, consultez [Gérer des groupes de ressources Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Emplacement :** Sélectionnez l’emplacement le plus proche de vos appareils.

      ![Entrée des informations de base sur votre instance de service Device Provisioning dans le panneau du Portail](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Cliquez sur le bouton de notification pour surveiller la création de l’instance de ressource. Une fois que le service est déployé, cliquez sur **Épingler au tableau de bord**, puis sur **Accéder à la ressource**.

    ![Notification Surveiller le déploiement](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Lier le IoT Hub à votre service d’approvisionnement d’appareil

Dans cette section, vous allez ajouter une configuration à l’instance de service Device Provisionings. Cette configuration définit l’IoT Hub pour lequel les périphériques seront configurés.

1. Cliquez sur le bouton **Toutes les ressources** dans le menu de gauche du portail Azure. Sélectionnez l’instance de service Device Provisioning que vous avez créée à la section précédente.  

2. Dans le panneau de résumé du service d’approvisionnement d’appareil, sélectionnez **Hubs IoT liés**. Cliquez sur le bouton **+ Ajouter** qui s’affiche dans la partie supérieure. 

3. Sur la page **Ajouter un lien au hub IoT**, fournissez les informations ci-après pour lier votre nouvelle instance de service Device Provisioning à un IoT Hub. Cliquez ensuite sur **Enregistrer**. 

    * **Abonnement :** Sélectionnez l’abonnement contenant le hub IoT que vous voulez lier à votre nouvelle instance du service Device Provisioning.
    * **Hub IoT :** Sélectionnez le hub IoT à lier à votre nouvelle instance du service Device Provisioning.
    * **Stratégie d’accès :** Sélectionnez **iothubowner** comme informations d’identification pour établir le lien avec le hub IoT.  

      ![Liaison du nom du Hub à lier à l’instance de service Device Provisioning dans le panneau du Portail](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Désormais le hub sélectionné devrait s’afficher sous le panneau **Hubs IoT liés**. Vous devrez peut-être cliquer sur **Actualiser** pour afficher **Hubs IoT liés**.



## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides ou les didacticiels, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées lors de ce démarrage rapide dans le portail Azure.

1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service d’approvisionnement d’appareil. Dans la partie supérieure du panneau **Toutes les ressources**, cliquez sur **Supprimer**.  
2. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Dans la partie supérieure du panneau **Toutes les ressources**, cliquez sur **Supprimer**.  

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce Démarrage rapide, vous avez déployé un IoT Hub et une instance de service Device Provisioning, puis vous avez lié ces deux ressources. Pour savoir comment utiliser cette configuration pour approvisionner un appareil simulé, référez-vous au démarrage rapide relatif à la création d’appareil simulé.

> [!div class="nextstepaction"]
> [Démarrage rapide pour créer un appareil simulé](./quick-create-simulated-device.md)
