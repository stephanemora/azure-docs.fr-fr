---
title: Fichier Include
description: Fichier Include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 6656c374ae0ff4aae7f99fd340e9e25e5cbc67d1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854205"
---
Vous pouvez maintenant ajouter des données à votre nouveau conteneur grâce à l’Explorateur de données.

1. Dans l’**Explorateur de données**, développez la base de données **Tâches**, puis développez le conteneur **Éléments**. Sélectionnez **Éléments**, puis cliquez sur **Nouvel élément**.

   ![Créer des documents dans l’Explorateur de données, dans le Portail Azure](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. À présent, ajoutez un document au conteneur avec la structure suivante.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Une fois le fichier json ajouté à l’onglet **Documents**, sélectionnez **Enregistrer**.

    ![Copiez les données json, puis sélectionnez Enregistrer dans l’Explorateur de données dans le portail Azure.](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Créez et enregistrez un document supplémentaire dans lequel vous insérez une valeur unique pour la propriété `id` et modifiez les autres propriétés selon vos besoins. Vos nouveaux documents peuvent avoir la structure de votre choix car Azure Cosmos DB n’impose aucun schéma pour vos données.