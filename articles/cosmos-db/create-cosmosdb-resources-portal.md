---
title: 'Démarrage rapide : Créer des ressources Azure Cosmos DB à partir du portail Azure'
description: Ce guide de démarrage rapide montre comment créer une base de données, un conteneur et des éléments Azure Cosmos à l’aide du Portail Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/19/2021
ms.openlocfilehash: df434c54bed103bd17d1a0009478e624da762ecc
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112235668"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Démarrage rapide : Créer un compte, une base de données, un conteneur et des éléments Azure Cosmos à partir du portail Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez utiliser Azure Cosmos DB pour créer et interroger rapidement des bases de données de paires clé/valeur, des bases de données de documents et des bases de données de graphes, qui bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale au cœur même d’Azure Cosmos DB. 

Ce guide de démarrage rapide montre comment utiliser le portail Azure pour créer un compte d’[API SQL](./introduction.md) Azure Cosmos DB, comment créer une base de données de documents et un conteneur, et comment ajouter des données au conteneur. 

## <a name="prerequisites"></a>Prérequis

Un abonnement Azure ou un compte d’essai gratuit Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Ajouter une base de données et un conteneur 

Vous pouvez utiliser l’Explorateur de données du portail Azure pour créer une base de données et un conteneur.

1. Dans la page de votre compte Azure Cosmos DB, sur la gauche, sélectionnez **Explorateur de données**, puis **Nouveau conteneur**. 

    Vous devrez peut-être faire défiler vers la droite pour voir la fenêtre **Ajouter un conteneur**.

    :::image type="content" source="./media/create-cosmosdb-resources-portal/add-database-container.png" alt-text="Explorateur de données du portail Azure, volet Ajouter un conteneur":::

1. Dans le volet **Ajouter un conteneur**, entrez les paramètres du nouveau conteneur.

    |Paramètre|Valeur suggérée|Description
    |---|---|---|
    |**ID de base de données**|ToDoList|Entrez *ToDoList* pour le nom de la nouvelle base de données. Les noms de base de données doivent comporter entre 1 et 255 caractères, et ne peuvent pas contenir les caractères `/, \\, #, ?`, ni un espace de fin. Cochez l’option **Partager le débit entre les conteneurs** qui vous permet de partager le débit provisionné sur la base de données entre tous les conteneurs au sein de la base de données. Cette option permet également de réduire les coûts. |
    | **Débit de la base de données**| Vous pouvez provisionner un débit **Mise à l’échelle automatique** ou **Manuel**. Le débit Manuel vous permet de mettre à l’échelle les RU/s vous-même, tandis que le débit Mise à l’échelle automatique permet au système d’adapter les RU/s en fonction de l’utilisation. Sélectionnez **Manuel** pour cet exemple. <br><br> Laissez le débit sur 400 unités de requête par seconde (RU/s). Si vous voulez réduire la latence, vous pouvez effectuer un scale-up du débit plus tard en estimant les RU/s avec la [calculatrice de capacité](estimate-ru-with-capacity-planner.md).<br><br>**Remarque** : Ce paramètre n’est pas disponible lors de la création d’un nouveau conteneur dans un compte serverless. |
    |**ID de conteneur**|Éléments|Entrez *Éléments* comme nom de votre nouveau conteneur. Les ID de conteneur sont soumis aux mêmes exigences en termes de caractères que les noms de base de données.|
    |**Clé de partition**| /category| L’exemple décrit dans cet article utilise */category* comme clé de partition.|

    Pour cet exemple, n’ajoutez pas de **clés uniques** et n’activez pas le **Magasin analytique**. Les clés uniques vous permettent d’ajouter une couche d’intégrité des données à la base de données en garantissant que chaque clé de partition contient des valeurs uniques. Pour plus d’informations, consultez [Clés uniques dans Azure Cosmos DB](unique-keys.md). Le [Magasin analytique](analytical-store-introduction.md) permet d’effectuer des analyses à grande échelle sur des données opérationnelles sans aucun impact sur vos charges de travail transactionnelles.

1. Sélectionnez **OK**. L’Explorateur de données présente la nouvelle base de données et le conteneur que vous avez créé.

## <a name="add-data-to-your-database"></a>Ajouter des données à votre base de données

Ajoutez des données à votre nouvelle base de données à l’aide de l’Explorateur de données.

1. Dans l’**Explorateur de données**, développez la base de données **ToDoList**, puis développez le conteneur **Éléments**. Ensuite, sélectionnez **Éléments**, puis sélectionnez **Nouvel élément**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Créer des documents dans l’Explorateur de données du portail Azure":::
   
1. Ajoutez la structure suivante au document, sur la droite du volet **Documents** :

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Sélectionnez **Enregistrer**.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Copie des données json et sélection de l’option Enregistrer dans l’Explorateur de données du portail Azure":::
   
1. Sélectionnez à nouveau **Nouveau document** puis créez et enregistrez un autre document avec un `id` unique, ainsi que les propriétés et valeurs souhaitées. Vos documents peuvent avoir la structure de votre choix, car Azure Cosmos DB n’impose aucun schéma à vos données.

## <a name="query-your-data"></a>Interroger vos données

[!INCLUDE [cosmos-db-create-sql-api-query-data](includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](includes/cosmos-db-delete-resource-group.md)]

Si vous souhaitez supprimer uniquement la base de données et utiliser le compte Azure Cosmos à l’avenir, vous pouvez supprimer la base de données en effectuant les étapes suivantes :

* Accédez à votre compte Azure Cosmos.
* Ouvrez l’**Explorateur de données**, cliquez avec le bouton droit sur la base de données que vous souhaitez supprimer et sélectionnez **Supprimer la de base de données**.
* Entrez l’ID et le nom de la base de données pour confirmer l’opération de suppression. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, ainsi qu’à créer une base de données et un conteneur à l’aide de l’Explorateur de données. Vous pouvez maintenant importer des données supplémentaires dans votre compte Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)