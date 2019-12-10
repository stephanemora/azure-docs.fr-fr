---
title: 'Didacticiel : Linux ASP.NET Core avec SQL DB'
description: Découvrez comment faire fonctionner une application Linux ASP.NET Core pilotée par les données dans Azure App Service en établissant une connexion à une instance SQL Database.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 67ea11b2e1457bf4a788f54664ed54ff7ca9c8d9
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688918"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Créer une application ASP.NET Core et SQL Database dans Azure App Service sur Linux

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Linux. Pour effectuer un déploiement dans App Service sur _Windows_, consultez [Générer une application .NET Core et SQL Database dans Azure App Service](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce tutoriel vous montre comment créer une application .NET Core et comment la connecter à une instance SQL Database. Quand vous avez terminé, vous disposez d’une application MVC .NET Core en cours d’exécution dans App Service sur Linux.

![Application exécutée dans App Service sur Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données SQL dans Azure
> * Connecter une application .NET Core à une instance SQL Database
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

* [Installez Git](https://git-scm.com/)
* [Installez le SDK .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="create-local-net-core-app"></a>Création de l’application .NET Core locale

Cette étape consiste à configurer le projet .NET Core local.

### <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Dans la fenêtre de terminal, `cd` vers un répertoire de travail.

Exécutez les commandes suivantes pour cloner l’exemple de référentiel et modifier son répertoire racine.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Cet exemple de projet contient une simple application CRUD (Create-Read-Update-Delete) basée sur [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Exécution de l'application

Exécutez la commande suivante pour installer les packages requis, migrer les bases de données et démarrer l’application.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Dans un navigateur, accédez à `http://localhost:5000`. Sélectionnez le lien **Create New** et créez quelques éléments _to-do_.

![se connecte correctement à SQL Database](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Pour arrêter .NET Core à tout moment, appuyez sur `Ctrl+C` dans le terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Création d’une instance SQL Database de production

Dans cette étape, vous allez créer une instance SQL Database dans Azure. Lorsque votre application est déployée sur Azure, elle utilise cette base de données cloud.

Pour l’instance SQL Database, ce tutoriel utilise [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Créer un serveur logique SQL Database

Dans Cloud Shell, créez un serveur logique Azure SQL Database avec la commande [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Remplacez l’espace réservé *\<server-name>* par un nom unique d’instance SQL Database. Ce nom est utilisé en tant que point de terminaison de l’instance SQL Database `<server-name>.database.windows.net`. Pour cette raison, le nom doit être unique sur l’ensemble des serveurs logiques dans Azure. Le nom ne peut contenir que des minuscules, des chiffres, le tiret -) et doit compter entre 3 et 50 caractères. En outre, remplacez  *\<db-username>* et  *\<db-password>* par le nom d’utilisateur et le mot de passe de votre choix. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Lorsque le serveur logique SQL Database est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server-name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server-name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configurer une règle de pare-feu du serveur

Créez une [règle de pare-feu au niveau du serveur Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) avec la commande [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Lorsque les adresses IP de début et de fin sont définies sur 0.0.0.0, le pare-feu est ouvert uniquement pour les autres ressources Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Créer une base de données

Créez une base de données avec un [niveau de performance S0](../../sql-database/sql-database-service-tiers-dtu.md) sur le serveur avec la commande [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Créer une chaîne de connexion

Remplacez la chaîne suivante par les valeurs *\<server-name>* , *\<db-username>* et *\<db-password>* utilisées précédemment.

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

Chaîne de connexion pour votre application .NET Core. Copiez-la pour une utilisation ultérieure.

## <a name="deploy-app-to-azure"></a>Déployer des applications dans Azure

Dans cette étape, vous déployez votre application .NET Core connectée à SQL Database sur App Service sur Linux.

### <a name="configure-local-git-deployment"></a>Configurer le déploiement Git local

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-connection-string"></a>Configurer la chaîne de connexion

Pour définir les chaînes de connexion de votre application Azure, utilisez la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell. Dans la commande suivante, remplacez *\<app-name>* ainsi que le paramètre *\<connection-string>* par la chaîne de connexion que vous avez créée précédemment.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

Dans ASP.NET Core, vous pouvez utiliser cette chaîne de connexion nommée (`MyDbConnection`) à l’aide du modèle standard, comme n’importe quelle chaîne de connexion spécifiée dans *appsettings. json*. Dans ce cas, `MyDbConnection` est également défini dans votre fichier *appsettings.json*. Lors de l’exécution dans App Service, la chaîne de connexion définie dans App Service est prioritaire sur celle qui est définie dans votre fichier *appsettings.json*. Le code utilise la valeur  *appsettings.json* pendant le développement local, et le même code utilise la valeur App Service lorsqu’elle est déployée.

Pour voir comment la chaîne de connexion est référencée dans votre code, consultez [Se connecter à SQL Database en production](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Configurer une variable d’environnement

Définissez ensuite le paramètre d’application `ASPNETCORE_ENVIRONMENT` sur _Production_. Ce paramètre vous permet de savoir si vous utilisez Azure, car vous utilisez SQLite pour votre environnement de développement local et SQL Database pour votre environnement Azure.

L’exemple suivant configure un paramètre d’application `ASPNETCORE_ENVIRONMENT` dans votre application Azure. Remplacez l’espace réservé *\<app-name>* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Pour voir comment la variable d’environnement est référencée dans votre code, consultez [Se connecter à SQL Database en production](#connect-to-sql-database-in-production).

### <a name="connect-to-sql-database-in-production"></a>Connexion à SQL Database en production

Dans votre référentiel local, ouvrez Startup.cs et recherchez le code suivant :

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Remplacez-le par le code suivant, qui utilise les variables d’environnement que vous avez configurées précédemment.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Si ce code détecte qu’il s’exécute en production (indiquant l’environnement Azure), il utilise alors la chaîne de connexion que vous avez configurée pour se connecter à SQL Database. Pour plus d’informations sur l’accès aux paramètres d’application dans App Service, consultez [Accéder aux variables d’environnement](configure-language-dotnetcore.md#access-environment-variables).

L’appel `Database.Migrate()` vous aide lorsqu’il est exécuté dans Azure, car il crée automatiquement les bases de données dont votre application .NET Core a besoin, en fonction de sa configuration de migration.

Enregistrez vos modifications, puis validez-les dans le référentiel Git.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Accéder à l’application Azure

Accédez à l’application déployée à l’aide de votre navigateur web.

```bash
http://<app-name>.azurewebsites.net
```

Ajoutez quelques tâches.

![Application exécutée dans App Service sur Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Félicitations !** Vous exécutez une application .NET Core pilotée par les données dans App Service sur Linux.

## <a name="update-locally-and-redeploy"></a>Mise à jour locale et redéploiement

Dans cette étape, vous apportez une modification à votre schéma de base de données et la publiez sur Azure.

### <a name="update-your-data-model"></a>Mettre à jour votre modèle de données

Ouvrez _Models\Todo.cs_ dans l’éditeur de code. Ajoutez la propriété suivante à la classe `ToDo` :

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Exécuter la fonction Code First Migrations en local

Exécutez quelques commandes pour mettre à jour votre base de données locale.

```bash
dotnet ef migrations add AddProperty
```

Mettez à jour la base de données locale :

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Utiliser la nouvelle propriété

Apportez quelques modifications à votre code pour utiliser la propriété `Done`. Pour plus de simplicité dans ce didacticiel, vous allez uniquement modifier les vues `Index` et `Create` pour voir la propriété en action.

Ouvrez _Controllers\TodosController.cs_.

Recherchez la méthode `Create()` et ajoutez `Done` à la liste des propriétés dans l’attribut `Bind`. Lorsque vous avez terminé, la signature de votre méthode `Create()` doit ressembler à ceci :

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Ouvrez _Views\Todos\Create.cshtml_.

Dans le code Razor, vous devriez voir un élément `<div class="form-group">` pour `Description` et un autre élément `<div class="form-group">` pour `CreatedDate`. Juste après ces deux éléments, ajoutez un autre élément `<div class="form-group">` pour `Done` :

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Ouvrez _Views\Todos\Index.cshtml_.

Recherchez l’élément `<th></th>` vide. Juste au-dessus de cet élément, ajoutez le code Razor suivant :

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Recherchez l’élément `<td>` contenant les assistances de balise `asp-action`. Juste au-dessus de cet élément, ajoutez le code Razor suivant :

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Voilà tout ce que vous avez à faire pour voir les modifications dans les vues `Index` et `Create`.

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

Exécutez l’application localement.

```bash
dotnet run
```

Dans votre navigateur, accédez à `http://localhost:5000/`. Vous pouvez maintenant ajouter un élément de tâche et cocher **Terminé**. Cette tâche devrait ensuite apparaître dans votre page d’accueil comme un élément terminé. N’oubliez pas que la vue `Edit` n’affiche pas le champ `Done`, car vous n’avez pas modifié la vue `Edit`.

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Une fois le `git push` terminé, accédez à votre application Azure et testez la nouvelle fonctionnalité.

![Application Azure après l’activation de Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Toutes les tâches existantes sont toujours affichées. Lorsque vous republiez votre application .NET Core, les données existantes dans votre instance SQL Database ne sont pas perdues. En outre, Entity Framework Core Migrations modifie uniquement le schéma de données, sans toucher à vos données existantes.

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

L’exemple de projet suit déjà les instructions fournies dans [Journalisation ASP.NET Core dans Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider), et apporte deux changements à la configuration :

- Il inclut une référence à `Microsoft.Extensions.Logging.AzureAppServices` dans *DotNetCoreSqlDb.csproj*.
- Il appelle `loggerFactory.AddAzureWebAppDiagnostics()` dans *Startup.cs*.

> [!NOTE]
> Le niveau de journalisation du projet est défini sur `Information` dans *appsettings.json*.
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Pour plus d’informations sur la personnalisation des journaux d’activité ASP.NET Core, consultez [Journalisation dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Gérer votre application Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis sur le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données SQL dans Azure
> * Connecter une application .NET Core à une instance SQL Database
> * Déploiement de l’application dans Azure
> * Mettre à jour le modèle de données et redéployer l’application
> * Diffuser des journaux d’activité à partir d’Azure vers votre terminal
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application.

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer une application ASP.NET Core](configure-language-dotnetcore.md)
