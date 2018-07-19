---
title: Événements de stockage d’objets blob pour Azure Event Grid avec le portail Azure| Microsoft Docs
description: Utilisez Azure Event Grid et le portail Azure pour créer un compte de stockage d’objets blob, et abonnez-vous à ses événements.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 07/05/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 423995aecc6e5f29464ad140349ba27f89c75b5d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068741"
---
# <a name="create-and-route-blob-storage-events-with-the-azure-portal-and-event-grid"></a>Créer et acheminer des événements de stockage d’objets blob avec le portail Azure et Event Grid

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans cet article, vous utilisez le portail Azure pour créer un compte de stockage d’objets blob, vous abonner aux événements de ce stockage d’objets blob et déclencher un événement pour afficher le résultat. En règle générale, vous envoyez des événements à un point de terminaison qui traite les données d’événement et entreprend des actions. Toutefois, pour simplifier cet article, vous envoyez les événements à une application web qui collecte et affiche les messages.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Une fois que vous avez fini, vous voyez que les données d’événement ont été envoyées à l’application web.

![Afficher les résultats](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Pour utiliser des événements de stockage Blob, vous avez besoin d’un [compte de stockage d’objets blob](../storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) ou d’un [compte de stockage v2 à usage général](../storage/common/storage-account-options.md#general-purpose-v2-accounts). Les comptes **v2 à usage général (GPv2)** sont des comptes de stockage qui prennent en charge toutes les fonctionnalités pour tous les services de stockage, notamment Objets BLOB, Fichiers, Files d’attente et Tables. Un **compte de stockage d’objets blob** est un compte de stockage spécialisé pour le stockage des données non structurées en tant qu’objets blob dans Stockage Azure. Les comptes de stockage d’objets blob sont comme vos comptes de stockage à usage général existants et offrent les excellents niveaux de durabilité, disponibilité, évolutivité et performances dont vous bénéficiez aujourd’hui. Ils assurent notamment la cohérence d’API à 100 % pour les objets blob de blocs et d’ajout. Pour les applications qui requièrent uniquement le stockage d’objets blob de blocs ou d’objets blob d’ajout, nous recommandons d’utiliser des comptes de stockage d’objets blob. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Pour créer un stockage d’objets blob, sélectionnez **Créer une ressource**. 

   ![Créer une ressource](./media/blob-event-quickstart-portal/create-resource.png)

1. Sélectionnez **Stockage** pour filtrer les options disponibles, puis **Compte de stockage - blob, fichier, table, file d’attente**.

   ![Sélectionner Stockage](./media/blob-event-quickstart-portal/create-storage.png)

1. Indiquez des valeurs pour le stockage d’objets blob, notamment un nom unique pour le compte. Sélectionnez **Stockage d’objets blob** comme type de compte. Sélectionnez l’un des [emplacements](overview.md) prenant en charge Event Grid. Après avoir défini les valeurs, sélectionnez **Créer**.

   ![Étapes de démarrage](./media/blob-event-quickstart-portal/provide-blob-values.png)

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de nous abonner aux événements du stockage d’objets blob, nous allons créer le point de terminaison pour le message de l’événement. En règle générale, le point de terminaison entreprend des actions en fonction des données d’événement. Pour simplifier ce guide de démarrage rapide, déployez une [application web prédéfinie](https://github.com/dbarkol/azure-event-grid-viewer) qui affiche les messages d’événement. La solution déployée comprend un plan App Service, une offre App Service Web Apps et du code source en provenance de GitHub.

1. Sélectionnez **Déployer sur Azure** pour déployer la solution sur votre abonnement. Dans le portail Azure, indiquez des valeurs pour les paramètres.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdbarkol%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Le déploiement peut prendre quelques minutes. Une fois le déploiement réussi, affichez votre application web pour vérifier qu’elle s’exécute. Dans un navigateur web, accédez à : `https://<your-site-name>.azurewebsites.net`

1. Vous voyez le site, mais aucun événement n’est encore posté sur celui-ci.

   ![Afficher le nouveau site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>S’abonner à un stockage d’objets blob

Vous vous abonnez à une rubrique pour communiquer à Event Grid les événements qui vous intéressent, et où les envoyer.

1. Dans le portail, sélectionnez votre stockage d’objets blob, puis **Événements**.

   ![Sélectionner Événements](./media/blob-event-quickstart-portal/select-events.png)

1. Pour envoyer des événements à votre application de visionneuse, utilisez un webhook pour le point de terminaison. Sélectionnez **Autres options**, puis **Webhook**.

   ![Sélectionner Webhook](./media/blob-event-quickstart-portal/select-web-hook.png)

1. L’abonnement aux événements est prérempli avec les valeurs de votre stockage d’objets blob. Pour le point de terminaison du webhook, indiquez l’URL de votre application web et ajoutez `api/updates` à l’URL de la page d’accueil. Donnez un nom à votre abonnement. Quand vous avez terminé, sélectionnez **Créer**.

   ![Sélectionner des journaux](./media/blob-event-quickstart-portal/create-subscription.png)

1. Affichez à nouveau votre application web, et notez qu’un événement de validation d’abonnement lui a été envoyé. Sélectionnez l’icône en forme d’œil pour développer les données d’événements. Event Grid envoie l’événement de validation pour que le point de terminaison puisse vérifier qu’il souhaite recevoir des données d’événement. L’application web inclut du code pour valider l’abonnement.

   ![Afficher l’événement d’abonnement](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison.

## <a name="send-an-event-to-your-endpoint"></a>Envoyer un événement à votre point de terminaison

Pour déclencher un événement pour le stockage d’objets blob, chargez un fichier. Celui-ci n’a pas besoin d’un contenu spécifique. L’article part du principe que vous disposez d’un fichier nommé testfile.txt, mais vous pouvez utiliser n’importe quel fichier.

1. Pour le stockage d’objets blob, sélectionnez **Objets blob**.

   ![Sélectionner Objets Blob](./media/blob-event-quickstart-portal/select-blobs.png)

1. Sélectionnez **+ Conteneur**. Donnez un nom à votre conteneur et utilisez n’importe quel niveau d’accès.

   ![Ajouter un conteneur](./media/blob-event-quickstart-portal/add-container.png)

1. Sélectionnez votre nouveau conteneur.

   ![Sélectionner un conteneur](./media/blob-event-quickstart-portal/select-container.png)

1. Pour charger un fichier, sélectionnez **Charger**.

   ![Sélectionner Télécharger](./media/blob-event-quickstart-portal/upload-file.png)

1. Accédez à votre fichier de test et chargez-le.

1. Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Quand vous examinez votre application web, vous pouvez remarquer qu’un événement créé par un objet blob a été reçu. 

  ```json
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/eventgroup/providers/Microsoft.Storage/storageAccounts/demoblob0625",
    "subject": "/blobServices/default/containers/eventcontainer/blobs/testfile.txt",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2018-06-25T22:50:41.1823131Z",
    "id": "89a2f9da-c01e-00bb-13d6-0c599506e4e3",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "41341a9b-e977-4a91-9000-c64125039047",
      "requestId": "89a2f9da-c01e-00bb-13d6-0c5995000000",
      "eTag": "0x8D5DAEE13C8F9ED",
      "contentType": "text/plain",
      "contentLength": 4,
      "blobType": "BlockBlob",
      "url": "https://demoblob0625.blob.core.windows.net/eventcontainer/testfile.txt",
      "sequencer": "00000000000000000000000000001C24000000000004712b",
      "storageDiagnostics": {
        "batchId": "ef633252-32fd-464b-8f5a-0d10d68885e6"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
  ```

## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous envisagez de continuer à utiliser cet événement, ne supprimez pas les ressources créées dans cet article. Dans le cas contraire, supprimez les ressources créées avec cet article.

Sélectionnez le groupe de ressources, puis **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer des rubriques et des abonnements d’événements personnalisés, vous pouvez en apprendre davantage sur Event Grid et ce qu’il peut vous offrir :

- [À propos d’Event Grid](overview.md)
- [Acheminer des événements de stockage Blob Azure vers un point de terminaison Web personnalisé ](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md)
