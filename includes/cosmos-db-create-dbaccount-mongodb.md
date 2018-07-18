---
title: Fichier Include
description: Fichier Include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 690c5f07a2b0f7a88e16f0b0bbbaa9ca78e37317
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728986"
---
1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, cliquez sur **Créer une ressource**, sur **Bases de données**, puis sous **Azure Cosmos DB**, cliquez sur **Créer**.
   
   ![Capture d’écran du portail Azure, mettant en surbrillance l’option Plus de services, et Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Dans le panneau **Nouveau compte**, indiquez **MongoDB** comme API et effectuez la configuration souhaitée pour le compte Azure Cosmos DB.
 
    * **ID** doit être un nom unique que vous voulez utiliser pour identifier votre compte Azure Cosmos DB. Il ne peut contenir que des lettres minuscules, des nombres, le caractère « - », et doit présenter entre 3 et 50 caractères.
    * **Abonnement** correspond à votre abonnement Azure. Il est rempli automatiquement.
    * **Groupe de ressources** correspond au nom du groupe de ressources pour votre compte Azure Cosmos DB. Sélectionnez **Créer**, puis entrez le nom du nouveau groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID.
    * **Emplacement** correspond à l’emplacement géographique de votre instance Azure Cosmos DB. Choisissez l’emplacement le plus proche de vos utilisateurs.

    Cliquez ensuite sur **Créer**.

    ![Page de nouveau compte pour Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. La création du compte prend quelques minutes. Attendez que le portail affiche la page **Félicitations ! Votre compte Azure Cosmos DB avec l’API MongoDB est prêt** page.

    ![Volet Notifications du portail Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
