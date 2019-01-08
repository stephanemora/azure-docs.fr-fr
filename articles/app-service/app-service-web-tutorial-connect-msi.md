---
title: Sécuriser la connexion SQL Database à l'aide d'une identité managée - Azure App Service | Microsoft Docs
description: Découvrez comment sécuriser la connectivité de la base de données à l’aide d’une identité managée, et comment appliquer cette technique à d’autres services Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6af6eb0dd6473b9fe947f7cc4939da4e0cbc77cb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718487"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutoriel : Sécuriser la connexion Azure SQL Database à partir d’App Service à l’aide d’une identité managée

[App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques dans Azure. Il offre également une [identité managée](overview-managed-identity.md) pour votre application, qui constitue une solution clé en main permettant de sécuriser l’accès à [Azure SQL Database](/azure/sql-database/) et à d’autres services Azure. Les identités managées dans App Service sécurisent votre application en en éliminant les secrets, par exemple les informations d’identification dans les chaînes de connexion. Dans ce tutoriel, vous allez ajouter l’identité managée à l’exemple d’application web ASP.NET que vous avez créé dans [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Lorsque vous aurez terminé, votre exemple d’application se connectera à SQL Database en toute sécurité sans nécessiter aucun nom d’utilisateur ni mot de passe.

> [!NOTE]
> Ce scénario est actuellement pris en charge par .NET Framework 4.6 et les versions ultérieures, mais pas par [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows). [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) prend en charge le scénario, mais il n’est pas encore inclus dans les images par défaut dans App Service. 
>

Vous apprendrez à :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * configurer le code de l’application pour l’authentification auprès de SQL Database à l’aide de l’authentification Azure Active Directory ;
> * Accorder des privilèges minimaux à l’identité managée dans SQL Database

> [!NOTE]
>L’authentification Azure Active Directory est _différente_ de [l’authentification Windows intégrée](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) dans Active Directory (AD DS) local. AD DS et Azure Active Directory utilisent des protocoles d’authentification totalement différents. Pour plus d’informations, consultez la [documentation sur Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Cet article se poursuit là où vous l'avez laissé dans [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Si vous ne l’avez pas encore fait, commencez par suivre ce didacticiel. Vous pouvez également adapter la procédure à votre propre application ASP.NET avec SQL Database.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-identities"></a>Activer les identités managées

Pour activer une identité managée pour votre application Azure, utilisez la commande [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) dans Cloud Shell. Dans la commande ci-après, remplacez *\<app name>* par votre propre valeur.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Voici un exemple de sortie après la création de l’identité dans Azure Active Directory :

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Vous utiliserez la valeur de `principalId` ci-dessus à l’étape suivante. Si vous souhaitez visualiser les détails de la nouvelle identité dans Azure Active Directory, exécutez la commande facultative ci-après avec la valeur de `principalId` :

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Accorder à l’identité un accès à la base de données

Maintenant, accordez à l’identité managée de votre application un accès à la base de données en utilisant la commande [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin_create) dans Cloud Shell. Dans la commande ci-après, remplacez *\<server_name>* et <principalid_from_last_step> par vos propres valeurs. Tapez un nom d’administrateur pour *\<admin_user>*.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

L’identité managée a à présent accès à votre serveur Azure SQL Database.

## <a name="modify-connection-string"></a>Modifier la chaîne de connexion

Modifiez la connexion que vous avez précédemment définie pour votre application à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans le Cloud Shell. Dans la commande ci-après, remplacez *\<app name>* par le nom de votre application, puis remplacez *\<server_name>* et *\<db_name>* par les valeurs de votre service SQL Database.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Modifier le code ASP.NET

Dans Visual Studio, ouvrez la Console du gestionnaire de package et ajoutez le package NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) :

```PowerShell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.1.0-preview
```

Ouvrez le fichier _Models\MyDatabaseContext.cs_, puis ajoutez les instructions `using` ci-après au début du fichier :

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

Dans la classe `MyDatabaseContext`, ajoutez le constructeur suivant :

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Ce constructeur configure un objet SqlConnection personnalisé pour l’utilisation d’un jeton d’accès pour Azure SQL Database à partir d’App Service. Votre application App Service utilise ce jeton d’accès pour s’authentifier auprès d’Azure SQL Database avec son identité managée. Pour plus d’informations, consultez la section [Obtention de jetons pour les ressources Azure](overview-managed-identity.md#obtaining-tokens-for-azure-resources). L’instruction `if` vous permet de continuer à tester votre application localement avec LocalDB.

> [!NOTE]
> Pour l’instant, `SqlConnection.AccessToken` est uniquement pris en charge dans .NET Framework 4.6 et les versions ultérieures, ainsi que [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2), mais pas dans [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows).
>

Pour utiliser ce nouveau constructeur, ouvrez `Controllers\TodosController.cs` et recherchez la ligne `private MyDatabaseContext db = new MyDatabaseContext();`. Le code existant utilise le contrôleur `MyDatabaseContext` par défaut pour créer une base de données à l’aide de la chaîne de connexion standard, qui contenait le nom d’utilisateur et le mot de passe en clair avant que vous ne [la modifiiez](#modify-connection-string).

Remplacez la ligne entière par le code suivant :

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new System.Data.SqlClient.SqlConnection());
```

### <a name="publish-your-changes"></a>Publier vos modifications

Il ne vous reste plus qu’à publier vos modifications sur Azure.

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **DotNetAppSqlDb**, puis sélectionnez **Publier**.

![Publier à partir de l’Explorateur de solutions](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Dans la page de publication, cliquez sur **Publier**. Lorsque la nouvelle page web affiche votre liste des tâches, votre application se connecte à la base de données à l’aide de l’identité managée.

![Application Azure après l’activation des Migrations Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Vous devriez désormais être en mesure de modifier la liste des tâches comme auparavant.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Accorder des privilèges minimaux à l’identité

Au cours des étapes précédentes, vous avez probablement remarqué que votre identité managée était connectée à SQL Server en tant qu’administrateur Azure AD. Pour accorder des privilèges minimaux à votre identité managée, connectez-vous au serveur Azure SQL Database en tant qu’administrateur Azure AD, puis ajoutez un groupe Azure Active Directory contenant cette identité managée. 

### <a name="add-managed-identity-to-an-azure-active-directory-group"></a>Ajouter l’identité managée à un groupe Azure Active Directory

Dans Cloud Shell, ajoutez l’identité managée de votre application dans un nouveau groupe Azure Active Directory nommé _myAzureSQLDBAccessGroup_, comme dans le script suivant :

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Si vous souhaitez visualiser la sortie JSON complète pour chaque commande, supprimez les paramètres `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Reconfigurer l’administrateur Azure AD

Vous avez jusqu’ici attribué l’identité managée en tant qu’administrateur Azure AD de votre service SQL Database. Vous ne pouvez pas utiliser cette identité pour une connexion interactive (en vue d’ajouter des utilisateurs de base de données) ; vous devez donc utiliser votre utilisateur Azure AD réel. Pour ajouter votre utilisateur Azure AD, suivez la procédure décrite à la section [Approvisionner un administrateur Azure Active Directory pour votre serveur Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server). 

> [!IMPORTANT]
> Une fois ajouté, ne supprimez pas cet administrateur Azure AD pour votre base de données SQL, sauf si vous souhaitez désactiver complètement l’accès Azure AD à la base de données SQL (à partir de tous les comptes Azure AD).
> 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Accorder des autorisations au groupe Azure Active Directory

Dans le Cloud Shell, connectez-vous à SQL Database en utilisant la commande SQLCMD. Remplacez _\<server\_name>_ par le nom de votre serveur SQL Database, puis _\<db\_name>_ par le nom de la base de données utilisée par votre application, _\<AADuser\_name>_ et _\<AADpassword>_ par les informations d’identification de votre utilisateur Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

À l’invite SQL de la base de données souhaitée, exécutez les commandes suivantes pour ajouter le groupe Azure Active Directory que vous avez créé précédemment et pour lui accorder les autorisations dont votre application a besoin. Par exemple, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Tapez `EXIT` pour revenir à l’invite Cloud Shell. 

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * configurer le code de l’application pour l’authentification auprès de SQL Database à l’aide de l’authentification Azure Active Directory ;
> * Accorder des privilèges minimaux à l’identité managée dans SQL Database

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à Azure App Service](app-service-web-tutorial-custom-domain.md)
