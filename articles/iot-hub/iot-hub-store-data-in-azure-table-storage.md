---
title: Enregistrer des messages IoT Hub dans le stockage de données Azure | Microsoft Docs
description: Utilisez le routage de messages IoT Hub pour enregistrer les messages IoT Hub dans votre stockage Blob Azure. Les messages IoT Hub contiennent des informations, notamment des données de capteurs, envoyées par l’appareil IoT.
author: rangv
manager: ''
keywords: stockage de données iot, stockage de données de capteur iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856288"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Enregistrer les messages IoT Hub qui contiennent des données de capteurs dans un stockage Blob Azure

![Diagramme de bout en bout](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenu

Vous allez découvrir comment créer un compte de stockage Azure et une application de fonction Azure pour stocker des messages IoT Hub dans un stockage d’objets blob Azure.

## <a name="what-you-do"></a>Procédure

- Créez un compte de stockage Azure.
- Configurez l’IoT Hub pour router les messages vers le stockage.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- [Configurez votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) de façon à couvrir les exigences suivantes :
  - Un abonnement Azure actif
  - Un hub IoT associé à votre abonnement 
  - Une application fonctionnelle qui envoie des messages à votre hub IoT

## <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource** > **Stockage** > **Compte de stockage**.

2. Entrez les informations nécessaires sur le compte de stockage :

   ![Créer un compte de stockage sur le Portail Azure](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **Name** : nom du compte de stockage. Le nom doit être globalement unique.

   * **Type de compte** : choisissez `Storage (general purpose v1)`.

   * **Emplacement** : choisissez le même emplacement que celui utilisé par votre IoT Hub.

   * **Réplication** : choisissez `Locally-redundant storage (LRS)`.

   * **Niveau de performance** : choisissez `Standard`.

   * **Transfert sécurisé requis** : choisissez `Disabled`.

   * **Abonnement** : sélectionnez votre abonnement Azure.

   * **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   * **Épingler au tableau de bord** : Sélectionnez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

3. Cliquez sur **Créer**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Préparer l’IoT Hub pour router les messages vers le stockage

IoT Hub en mode natif prend en charge le routage des messages vers le stockage Azure en tant qu’objets blob. Pour en savoir plus sur les points de terminaison personnalisés de Azure IoT Hub, vous pouvez faire référence à la [liste de points de terminaison IoT Hub intégrés](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints).

### <a name="add-storage-as-a-custom-endpoint"></a>Ajouter un stockage en tant qu’un point de terminaison personnalisé

1. Accédez à votre IoT Hub dans le portail Azure. 

2. Cliquez sur **Points de terminaison** > **Ajouter**. 

3. Nommez le point de terminaison et sélectionnez **Conteneur de stockage Azure** comme type de point de terminaison. 

4. Utilisez le sélecteur pour sélectionner le compte de stockage que vous avez créé dans la section précédente. Créez un conteneur de stockage et sélectionnez-le, puis cliquez sur **OK**.

   ![Créer un point de terminaison personnalisé dans IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Ajouter un itinéraire pour router les données vers le stockage

1. Cliquez sur **Itinéraires** > **Ajouter** et attribuez un nom à l’itinéraire. 

2. Sélectionnez **Messages des appareils** comme source de données, puis sélectionnez le point de terminaison de stockage que vous venez de créer en tant que point de terminaison de l’itinéraire. 

3. Entrez `true` en tant que chaîne de requête, puis cliquez sur **Enregistrer**.

   ![Créer un itinéraire dans IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Ajouter un itinéraire pour la télémétrie de chemin réactif (facultatif)

Par défaut, IoT Hub achemine tous les messages qui ne correspondent à aucun autre itinéraire vers le point de terminaison intégré. Étant donné que tous les messages de télémétrie correspondent désormais à la règle qui achemine les messages vers le stockage, vous devez ajouter un autre itinéraire pour les messages à écrire au point de terminaison intégré. Aucun frais supplémentaire n’est appliqué pour acheminer les messages vers plusieurs points de terminaison.

> [!NOTE]
> Vous pouvez ignorer cette étape si vous n’effectuez aucun traitement supplémentaire de vos messages de télémétrie.

1. Cliquez sur **Ajouter** dans le volet Itinéraires et attribuez un nom à l’itinéraire. 

2. Sélectionnez **Messages des appareils** comme source de données et **events** comme point de terminaison. 

3. Entrez `true` en tant que chaîne de requête, puis cliquez sur **Enregistrer**.

  ![Créer un itinéraire de chemin réactif dans IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Vérifier votre message dans votre conteneur de stockage

1. Exécutez l’exemple d’application sur votre appareil pour envoyer des messages à votre IoT Hub.

2. [Téléchargez et installez l’Explorateur de Stockage Azure](http://storageexplorer.com/).

3. Ouvrez l’Explorateur de Stockage, cliquez sur **Ajouter un compte Azure** > **Se connecter**, puis connectez-vous à votre compte Azure.

4. Cliquez sur votre abonnement Azure > **Comptes de stockage** > votre compte de stockage > **Conteneurs d’objets blob** > votre conteneur.

   Vous devriez voir les messages envoyés par votre appareil à votre IoT Hub consignés dans le conteneur d’objets blob.

## <a name="clean-up-resources"></a>Supprimer les ressources 

Dans ce tutoriel, vous avez ajouté un compte de stockage et le routage des messages à partir du IoT Hub à écrire dans le compte de stockage. Pour nettoyer les ressources créées, vous supprimez les informations de routage, puis supprimez le compte de stockage. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **Groupes de ressources** et sélectionnez le groupe de ressources utilisé. La liste des ressources dans le groupe s’affiche. 

   > [!NOTE]
   > Si vous souhaitez supprimer toutes les ressources du groupe de ressources, cliquez sur **Supprimer** pour supprimer le groupe de ressources, puis suivez les instructions. Cela supprime tous les éléments de ce groupe de ressources, afin que vous ayez terminé le nettoyage des ressources et que vous puissiez passer à la section suivante.

3. Cliquez sur le hub IoT que vous avez utilisé pour ce tutoriel. 

4. Dans le volet de IoT Hub, cliquez sur **Itinéraires**. Cochez la case en regard de la règle de routage ajoutée, puis cliquez sur **Supprimer**. Lorsqu’il vous est demandé si vous voulez supprimer cet itinéraire, cliquez sur **Oui**.

   ![Supprimer la règle de routage](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   Fermez le volet Routage. Vous revenez au volet Groupe de ressources.

5. Cliquez de nouveau sur le hub IoT. 

6. Dans le volet IoT Hub, cliquez sur **Points de terminaison**. Cochez la case en regard du point de terminaison que vous avez ajouté pour le conteneur de stockage, puis cliquez sur **Supprimer**. Lorsqu’il vous est demandé si vous voulez supprimer le point de terminaison sélectionné, cliquez sur **Oui**.

    ![Supprimer un point de terminaison](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    Fermez le volet Points de terminaison. Vous revenez au volet Groupe de ressources. 

7.  Cliquez sur le compte de stockage que vous configurez pour ce tutoriel. 

8.  Dans le volet Compte de stockage, cliquez sur **Supprimer** pour supprimer le compte de stockage. Vous êtes redirigé vers le volet **Supprimer le compte de stockage**.

   ![Supprimer le compte de stockage](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  Tapez le nom du compte de stockage, puis cliquez sur **Supprimer** au bas du volet. 

## <a name="next-steps"></a>Étapes suivantes

Vous venez de créer votre compte de stockage Azure et d’acheminer les messages à partir de IoT Hub vers un conteneur d’objets blob de ce compte de stockage.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
