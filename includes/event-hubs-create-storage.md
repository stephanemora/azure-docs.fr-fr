---
title: Fichier Include
description: Fichier Include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 690e0da160042b7ac432b1a36c024a8a8f439823
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125064"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>Créer un compte de stockage pour Event Processor Host
Event Processor Host est un agent intelligent qui simplifie la réception d’événements provenant d’Event Hubs en gérant les points de contrôle persistants et les destinataires parallèles. Pour les points de contrôle, Event Processor Host nécessite un compte de stockage. L’exemple suivant montre comment créer un compte de stockage et comment obtenir ses clés pour l’accès :

1. Dans le portail Azure, sélectionnez **Créer une ressource** en haut à gauche de l’écran.

2. Sélectionnez **Stockage**, puis **Compte de stockage - blob, fichier, table, file d’attente**.
   
    ![Sélectionner un compte de stockage](./media/event-hubs-create-storage/create-storage1.png)

3. Sur la page **Créer un compte de stockage**, procédez comme suit : 

   1. Nommez le compte de stockage. 
   2. Choisissez un abonnement Azure qui contient l’Event Hub.
   3. Sélectionnez le groupe de ressources qui contient l’Event Hub.
   4. Sélectionnez un emplacement dans lequel stocker la ressource. 
   5. Puis, cliquez sur **Vérifier + créer**.
   
      ![Créer un compte de stockage - page](./media/event-hubs-create-storage/create-storage2.png)

4. Sur la page **Vérifier + créer**, vérifiez les valeurs, puis sélectionnez **Créer**. 

    ![Vérifier les paramètres de compte de stockage et créer](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Une fois que vous voyez le **déploiements réussis** message, sélectionnez **accéder à la ressource** en haut de la page. Vous pouvez également afficher la page Compte de stockage en sélectionnant votre compte de stockage dans la liste des ressources.  

    ![Sélectionner le compte de stockage dans le déploiement](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. Dans la fenêtre **Bases**, sélectionnez **Objets blob**. 

    ![Sélectionner le service Blobs](./media/event-hubs-create-storage/select-blobs-service.png)
1. Sélectionnez **+ Conteneur** en haut, entrez le **nom** du conteneur, puis sélectionnez **OK**. 

    ![Création d’un conteneur d’objets blob](./media/event-hubs-create-storage/create-blob-container.png)
1. Sélectionnez **Clés d’accès** dans le menu de gauche, puis copiez la valeur de **key1**. 

    Enregistrez les valeurs suivantes dans le Bloc-notes ou un autre emplacement temporaire.
    - Nom du compte de stockage
    - Clé d’accès au compte de stockage
    - Nom du conteneur
