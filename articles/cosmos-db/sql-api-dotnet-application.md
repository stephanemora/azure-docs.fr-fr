---
title: 'Tutoriel ASP.NET Core MVC pour Azure Cosmos DB : Développement d’applications web'
description: Tutoriel ASP.NET Core MVC pour créer une application web MVC à l’aide d’Azure Cosmos DB. Vous allez stocker JSON et accéder aux données à partir d’une application todo hébergée sur Azure App Service - Tutoriel étape par étape ASP NET Core MVC.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 9824e1468604763834e37abe94290d68d81077ab
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020130"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Didacticiel : Développer une application web ASP.NET Core MVC avec Azure Cosmos DB à l’aide du kit SDK .NET 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Ce tutoriel vous montre comment utiliser Azure Cosmos DB pour stocker des données et y accéder à partir d’une application ASP.NET MVC qui est hébergée sur Azure. Dans ce tutoriel, vous allez utiliser le SDK .NET V3. L’image suivante illustre la page web que vous allez créer à l’aide de l’exemple de cet article :
 
![Capture d’écran de l’application web MVC de liste des tâches (todo) créée dans ce tutoriel - Tutoriel étape par étape pour ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Si vous n’avez pas le temps de suivre ce tutoriel, vous pouvez télécharger l’exemple de projet complet à partir de [GitHub][GitHub].

Ce didacticiel contient les sections suivantes :

> [!div class="checklist"]
> * Création d’un compte Azure Cosmos
> * Création d’une application ASP.NET Core MVC
> * Connexion de l’application à Azure Cosmos DB 
> * Réalisation d’opérations CRUD sur les données

> [!TIP]
> Ce tutoriel part du principe que vous avez déjà utilisé ASP.NET Core MVC et Azure App Service. Si vous débutez avec ASP.NET Core ou avec les [outils prérequis](#prerequisites), nous vous recommandons de télécharger l’exemple de projet complet à partir de [GitHub][GitHub], d’ajouter les packages NuGet nécessaires et de l’exécuter. Une fois le projet créé, vous pouvez consulter cet article pour obtenir des informations sur le code dans le contexte du projet.

## <a name="prerequisites"></a>Configuration requise 

Avant de suivre les instructions de cet article, veillez à disposer des ressources suivantes :

* **Un compte Azure actif :** Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Toutes les captures d’écran de cet article ont été réalisées à l’aide de Microsoft Visual Studio Community 2019. Si votre système est configuré avec une version différente, il est possible que vos écrans et options ne correspondent pas totalement. Toutefois, si vous respectez les conditions préalables ci-dessus, cette solution devrait fonctionner.

## <a name="create-an-azure-cosmos-account"></a>Étape 1 : Créer un compte Azure Cosmos

Commençons par créer un compte Azure Cosmos. Si vous disposez déjà d’un compte d’API SQL Azure Cosmos DB, ou si vous utilisez l’émulateur Azure Cosmos DB pour ce tutoriel, vous pouvez passer à la section [Créer une application ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Dans la section suivante, vous allez créer une application ASP.NET Core MVC. 

## <a name="create-a-new-mvc-application"></a>Étape 2 : Créer une application ASP.NET Core MVC

1. Dans Visual Studio, à partir du menu **Fichier**, choisissez **Nouveau**, puis sélectionnez **Projet**. La boîte de dialogue **Nouveau projet** apparaît.

2. Dans la fenêtre **Nouveau projet**, utilisez la zone de texte **Rechercher des modèles** pour rechercher « Web », puis sélectionnez **Application web ASP.NET Core**. 

   ![Créer un projet d’application web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Dans la zone **Nom** , tapez le nom du projet. Ce didacticiel utilise le nom « todo ». Si vous choisissez d’utiliser un autre nom, corrigez les exemples de code fournis chaque fois que ce tutoriel fait référence à l’espace de noms todo, de façon à utiliser le nom que vous avez attribué à votre application. 

4. Sélectionnez **Parcourir** pour accéder au dossier où vous souhaitez créer le projet. Sélectionnez **Create** (Créer). 

5. La boîte de dialogue **Créer une application web ASP.NET Core** s’affiche. Dans la liste des modèles, sélectionnez **Application web (Model-View-Controller)** .

6. Sélectionnez **Créer** et laissez Visual Studio structurer le modèle ASP.NET Core MVC vide. 

7. Lorsque Visual Studio a terminé la création de l’application MVC réutilisable, vous disposez d’une application ASP.NET vide que vous pouvez exécuter localement.

## <a name="add-nuget-packages"></a>Étape 3 : Ajouter le package NuGet Azure Cosmos DB au projet

Maintenant que nous avons presque tout le code de framework ASP.NET Core MVC dont nous avons besoin pour cette solution, ajoutons les packages NuGet nécessaires pour se connecter à Azure Cosmos DB.

1. Le Kit de développement logiciel (SDK) .NET Azure Cosmos DB est empaqueté et distribué en tant que package NuGet. Pour obtenir le package NuGet dans Visual Studio, utilisez le gestionnaire de package NuGet dans Visual Studio en cliquant avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis en sélectionnant **Gérer les packages NuGet**.
   
   ![Capture d’écran des options contextuelles pour le projet d’application web dans l’Explorateur de solutions, avec Gérer les packages NuGet mis en surbrillance.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. La boîte de dialogue **Gérer les packages NuGet** apparaît. Dans la zone **Parcourir** de NuGet, tapez **Microsoft.Azure.Cosmos**. À partir des résultats, installez le package **Microsoft.Azure.Cosmos**. Il télécharge et installe le package Azure Cosmos DB et ses dépendances. Sélectionnez **J’accepte** dans la fenêtre **Acceptation de la licence** pour effectuer l’installation.
   
   Vous pouvez aussi utiliser la console du Gestionnaire de package pour installer le package NuGet. Pour cela, dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du Gestionnaire de package**. À l'invite de commande, tapez la commande suivante :
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Une fois le package installé, votre projet Visual Studio doit contenir la référence de bibliothèque à Microsoft.Azure.Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Étape 4 : Configurer l’application ASP.NET Core MVC

À présent, ajoutons les modèles, vues et contrôleurs à cette application MVC :

* [Ajout d'un modèle](#add-a-model).
* [Ajout d'un contrôleur](#add-a-controller).
* [Ajout de vues](#add-views).

### <a name="add-a-model"></a> Ajouter un modèle

1. À partir de l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Modèles**, sélectionnez **Ajouter**, puis **Classe**. La boîte de dialogue **Ajouter un nouvel élément** s'affiche.

1. Nommez votre nouvelle classe **Item.cs** et sélectionnez **Ajouter**. 

1. Ensuite, remplacez le code de la classe « Item.cs » par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Les données stockées dans Azure Cosmos DB sont transmises et stockées au format JSON. Pour contrôler la façon dont vos objets sont sérialisés/désérialisés par JSON.NET, vous pouvez utiliser l’attribut **JsonProperty**, comme indiqué dans la classe **Item** que vous avez créée. En plus de contrôler le format du nom de propriété qui est transmis à JSON, vous pouvez renommer vos propriétés .NET, comme vous l’avez fait avec la propriété **Completed**. 

### <a name="add-views"></a>Ajout de vues

Ensuite, créons les trois vues suivantes : 

* [Ajout d’une vue d’éléments de liste](#AddItemIndexView).
* [Ajout d’une vue de nouvel élément](#AddNewIndexView).
* [Ajout d’une vue de modification d’élément](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Ajouter une vue d’éléments de liste

1. Dans **l’Explorateur de solutions**, développez le dossier **Vues**, cliquez avec le bouton droit sur le dossier vide **Élément** créé automatiquement par Visual Studio quand vous avez ajouté **ItemController** précédemment ; cliquez ensuite sur **Ajouter**, puis sur **Vue**.
   
   ![Capture d’écran de l’Explorateur de solutions présentant le dossier créé par Visual Studio avec les commandes Ajouter/Vue mises en surbrillance](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. Dans la boîte de dialogue **Ajouter une vue**, mettez à jour les valeurs suivantes :
   
   * Dans la zone **Nom de la vue**, tapez ***Index***.
   * Dans la zone **Modèle**, sélectionnez ***Liste***.
   * Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
   * Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
     
   ![Capture d'écran présentant la boîte de dialogue Ajouter une vue](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Après avoir ajouté ces valeurs, sélectionnez **Ajouter** et laissez Visual Studio créer une vue de modèle. Une fois terminé, il ouvre le fichier cshtml qui est créé. Vous pouvez fermer ce fichier dans Visual Studio. Vous y reviendrez ultérieurement.

#### <a name="AddNewIndexView"></a>Ajouter une vue de nouvel élément

De la même manière que vous avez créé une vue pour lister des éléments, créez une vue qui permet de créer des éléments en suivant ces étapes :

1. À partir de l’**Explorateur de solutions**, cliquez de nouveau avec le bouton droit sur le dossier **Élément**, sélectionnez **Ajouter**, puis **Vue**.

1. Dans la boîte de dialogue **Ajouter une vue**, mettez à jour les valeurs suivantes :
   
   * Dans la zone **Nom de la vue**, tapez ***Create***.
   * Dans la zone **Modèle**, sélectionnez ***Create***.
   * Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
   * Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
   * Sélectionnez **Ajouter**.
   
#### <a name="AddEditIndexView"></a>Ajouter une vue de modification d’élément

Et finalement, ajoutez une vue qui permet de modifier un élément en suivant ces étapes :

1. À partir de l’**Explorateur de solutions**, cliquez de nouveau avec le bouton droit sur le dossier **Élément**, sélectionnez **Ajouter**, puis **Vue**.

1. Dans la boîte de dialogue **Ajouter une vue** , procédez comme suit :
   
   * Dans la zone **Nom de la vue**, tapez ***Edit***.
   * Dans la zone **Modèle**, sélectionnez ***Edit***.
   * Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
   * Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
   * Sélectionnez **Ajouter**.

Une fois cette opération effectuée, fermez tous les documents cshtml dans Visual Studio. Vous reviendrez à ces vues plus tard.

### <a name="add-a-controller"></a>Ajout d'un contrôleur

1. À partir de l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Contrôleurs**, sélectionnez **Ajouter**, puis **Contrôleur**. La boîte de dialogue **Ajouter une structure** s'affiche.

1. Sélectionnez **Contrôleur MVC - Vide**, puis **Ajouter**.

   ![Capture d’écran de la boîte de dialogue Ajouter une structure avec l’option Contrôleur MVC - Vide mise en évidence](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nommez votre nouveau contrôleur, **ItemController** et remplacez le code de ce fichier par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   L’attribut **ValidateAntiForgeryToken** est utilisé ici pour protéger cette application contre les attaques de type falsification de requête intersites. Et il ne s’agit pas que d’un simple ajout de cet attribut, vos vues devraient également fonctionner avec ce jeton anti-falsification. Pour plus d’informations sur le sujet, et pour obtenir des exemples illustrant une implémentation adéquate, consultez [Prévention des falsifications de requête intersites][Preventing Cross-Site Request Forgery]. Le code source fourni sur [GitHub][GitHub] comporte l’implémentation complète.

   Nous utilisons également l’attribut **Bind** sur le paramètre de méthode pour établir une protection contre les attaques par sur-validation. Pour plus d’informations, consultez [Opérations CRUD de base dans ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Étape 5 : Se connecter à Azure Cosmos DB 

Maintenant que nous nous sommes occupés des éléments de base de MVC, ajoutons le code pour se connecter à Azure Cosmos DB et effectuer des opérations CRUD. 

### <a name="perform-crud-operations"></a> Effectuer des opérations CRUD sur les données

La première chose à faire ici est d’ajouter une classe qui contient la logique permettant de se connecter à Azure Cosmos DB et de l’utiliser. Pour ce tutoriel, nous allons encapsuler cette logique dans une classe nommée `CosmosDBService` et une interface nommée `ICosmosDBService`. Ce service effectue les opérations de flux de lecture et CRUD, telles que l’affichage des éléments incomplets, la création, la modification et la suppression des éléments. 

1. À partir de l’**Explorateur de solutions**, créez un dossier sous votre projet nommé **Services**.

1. Cliquez avec le bouton droit sur le dossier **Services**, sélectionnez **Ajouter**, puis **Classe**. Nommez la nouvelle classe **CosmosDBService** et sélectionnez **Ajouter**.

1. Ajoutez le code suivant à la classe **CosmosDBService** et remplacez le code de ce fichier par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Répétez les étapes 2-3, mais cette fois-ci pour une classe nommée **ICosmosDBService**, puis ajoutez le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Le code précédent reçoit un `CosmosClient` dans le cadre du constructeur. Suivant le pipeline ASP.NET Core, nous devons accéder au fichier **Startup.cs** du projet et initialiser le client en fonction de la configuration en tant qu’instance singleton à injecter via l’[Injection de dépendances](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Dans le gestionnaire **ConfigureServices**, nous définissons :

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. Dans le même fichier, nous définissons notre méthode d’assistance **InitializeCosmosClientInstanceAsync**, qui lira la configuration et initialisera le client.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)] 

1. La configuration est définie dans le fichier **appsettings.json** du projet. Ouvrez-le et ajoutez une section nommée **CosmosDb** :

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Maintenant, si vous exécutez l’application, le pipeline d’ASP.NET Core instanciera **CosmosDbService** et conservera une seule instance comme Singleton. Quand **ItemController** sera utilisé pour traiter les requêtes côté client, il recevra cette instance unique et pourra l’utiliser pour effectuer des opérations CRUD.

Si vous générez et exécutez ce projet maintenant, vous devriez voir ce qui suit :

![Capture d’écran de l’application web de liste des tâches (todo list) créée dans ce tutoriel de base de données](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Étape 6 : Exécuter l’application localement

Pour tester l’application sur votre machine locale, procédez comme suit :

1. Appuyez sur F5 dans Visual Studio pour générer l’application en mode débogage. Cette opération doit générer l'application et lancer un navigateur avec la page de grille vide que nous avons vue auparavant :
   
   ![Capture d’écran de l’application web de liste des tâches créée par ce tutoriel](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Cliquez sur le lien **Créer nouveau** et ajoutez des valeurs aux champs **Nom** et **Description**. Ne cochez pas la case **Terminé**, sinon le nouvel élément est ajouté avec l’état terminé et n’apparaît pas dans la liste initiale.
   
3. Cliquez sur **Créer**. Vous êtes alors redirigé vers la vue **Index**, et votre élément apparaît dans la liste. Vous pouvez ajouter quelques éléments supplémentaires à votre liste.

    ![Capture d'écran de la vue Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Cliquez sur **Modifier** en regard d’un **élément** de la liste. Vous êtes alors dirigé vers la vue **Edit** où vous pouvez mettre à jour les propriétés de votre objet, notamment l’indicateur **Completed**. Si vous marquez l’indicateur **Complete** et cliquez sur **Enregistrer**, l’**Élément** est affiché comme terminé dans la liste.
   
   ![Capture d’écran de la vue Index avec la case Terminé cochée](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. Vous pouvez vérifier à tout moment l’état des données dans le service Azure Cosmos DB à l’aide de [Cosmos Explorer](https://cosmos.azure.com) ou de l’explorateur de données de l’émulateur Azure Cosmos DB.

6. Une fois que vous avez testé l'application, appuyez sur Ctrl+F5 pour arrêter le débogage de l'application. Vous êtes prêt à déployer.

## <a name="deploy-the-application-to-azure"></a>Étape 7 : Déployer l’application 
Maintenant que l’application complète fonctionne correctement avec Azure Cosmos DB, nous allons déployer cette application web vers Azure App Service.  

1. Pour publier cette application, dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet et sélectionnez **Publier**.
   
2. Dans la boîte de dialogue **Publier**, sélectionnez **App Service**, puis **Créer** pour créer un profil App Service, ou choisissez **Sélectionner** pour utiliser un profil existant.

3. Si vous possédez un profil Azure App Service, sélectionnez un **Abonnement** dans la liste déroulante. Utilisez le filtre **Vue** pour trier par groupe de ressources ou type de ressource. Recherchez ensuite le service Azure App Service voulu et sélectionnez **OK**.
   
   ![Boîte de dialogue App Service dans Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Pour créer un nouveau profil Azure App Service, cliquez sur **Créer** dans la boîte de dialogue **Publier** . Dans la boîte de dialogue **Créer App Service**, entrez le nom de votre application web ainsi que l’abonnement, le groupe de ressources et le plan App Service appropriés, puis sélectionnez **Créer**.

   ![Créer une boîte de dialogue App Service dans Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

En l’espace de quelques secondes, Visual Studio publie votre application web et lance un navigateur dans lequel vous pouvez voir votre projet s’exécuter dans Azure !

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez découvert comment créer une application web ASP.NET Core MVC qui peut accéder aux données stockées dans Azure Cosmos DB. Vous pouvez maintenant passer à l’article suivant :

* [En savoir plus sur le partitionnement de vos données dans Azure Cosmos DB](./partitioning-overview.md)
* [En savoir plus sur la façon d’effectuer des requêtes plus avancées dans Azure Cosmos DB](./how-to-sql-query.md)
* [En savoir plus sur la modélisation de vos données dans un scénario plus avancé](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
