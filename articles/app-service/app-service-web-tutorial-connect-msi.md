---
title: Sécuriser la connexion Azure SQL Database à partir d’App Service à l’aide d’une identité de service managée | Microsoft Docs
description: Découvrez comment sécuriser la connectivité de la base de données à l’aide d’une identité de service managée, et comment appliquer cette technique à d’autres services Azure.
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
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 173588c0200666c52f3ac0a5d2e70d667cfe3294
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445559"
---
# <a name="tutorial-secure-sql-database-connection-with-managed-service-identity"></a>Didacticiel : sécuriser la connexion SQL Database avec une identité de service managée

[App Service](app-service-web-overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques dans Azure. Ce service offre également une [identité de service managée](app-service-managed-service-identity.md) pour votre application, qui constitue une solution clé en main pour la sécurisation de l’accès à [Azure SQL Database](/azure/sql-database/) et à d’autres services Azure. Les identités de service managées dans App Service sécurisent votre application en éliminant les secrets de cette dernière, telles que les informations d’identification dans les chaînes de connexion. Dans ce didacticiel, vous allez ajouter l’identité de service managée à l’exemple d’application web ASP.NET que vous avez créé dans le cadre du [Tutoriel : Création d’une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Lorsque vous aurez terminé, votre exemple d’application se connectera à SQL Database en toute sécurité sans nécessiter aucun nom d’utilisateur ni mot de passe.

Vous apprendrez à :

> [!div class="checklist"]
> * activer une identité de service managée ;
> * accorder à l’identité de service un accès à SQL Database ;
> * configurer le code de l’application pour l’authentification auprès de SQL Database à l’aide de l’authentification Azure Active Directory ;
> * accorder des privilèges minimaux à l’identité de service dans SQL Database.

> [!NOTE]
> L’authentification Azure Active Directory est _différente_ de [l’authentification Windows intégrée](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) dans Active Directory (AD DS) local. AD DS et Azure Active Directory utilisent des protocoles d’authentification totalement différents. Pour plus d’informations, consultez [The difference between Windows Server AD DS and Azure AD](../active-directory/fundamentals/understand-azure-identity-solutions.md#the-difference-between-windows-server-ad-ds-and-azure-ad) (Différence entre Windows Server AD DS et Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Cet article reprend à l’endroit où s’interrompait le [Tutoriel : Création d’une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Si vous ne l’avez pas encore fait, commencez par suivre ce didacticiel. Vous pouvez également adapter la procédure à votre propre application ASP.NET avec SQL Database.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-service-identity"></a>Activer une identité de service managée

Pour activer une identité de service pour votre application Azure, utilisez la commande [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) dans le service Cloud Shell. Dans la commande ci-après, remplacez *\<app name>* par votre propre valeur.

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

Vous allez à présent accorder à l’identité de service de votre application un accès à la base de données en utilisant la commande [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin_create) dans le Cloud Shell. Dans la commande ci-après, remplacez *\<server_name>* et <principalid_from_last_step> par vos propres valeurs. Tapez un nom d’administrateur pour *\<admin_user>*.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

L’identité de service managée a désormais accès à votre serveur Azure SQL Database.

## <a name="modify-connection-string"></a>Modifier la chaîne de connexion

Modifiez la connexion que vous avez précédemment définie pour votre application à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans le Cloud Shell. Dans la commande ci-après, remplacez *\<app name>* par le nom de votre application, puis remplacez *\<server_name>* et *\<db_name>* par les valeurs de votre service SQL Database.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Modifier le code ASP.NET

Dans votre projet **DotNetAppSqlDb** dans Visual Studio, ouvrez _packages.config_ et ajoutez la ligne ci-après à la liste des packages.

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
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

Ce constructeur configure un objet SqlConnection personnalisé pour l’utilisation d’un jeton d’accès pour Azure SQL Database à partir d’App Service. Votre application App Service utilise ce jeton d’accès pour s’authentifier auprès d’Azure SQL Database avec son identité de service managée. Pour plus d’informations, consultez la section [Obtention de jetons pour les ressources Azure](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources). L’instruction `if` vous permet de continuer à tester votre application localement avec LocalDB.

> [!NOTE]
> Pour l’instant, `SqlConnection.AccessToken` est uniquement pris en charge dans .NET Framework 4.6 et les versions ultérieures, et non dans [.NET Core](https://www.microsoft.com/net/learn/get-started/windows).
>

Pour utiliser ce nouveau constructeur, ouvrez `Controllers\TodosController.cs` et recherchez la ligne `private MyDatabaseContext db = new MyDatabaseContext();`. Le code existant utilise le contrôleur `MyDatabaseContext` par défaut pour créer une base de données à l’aide de la chaîne de connexion standard, qui contenait le nom d’utilisateur et le mot de passe en clair avant que vous ne [la modifiiez](#modify-connection-string).

Remplacez la ligne entière par le code suivant :

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>Publier vos modifications

Il ne vous reste plus qu’à publier vos modifications sur Azure.

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **DotNetAppSqlDb**, puis sélectionnez **Publier**.

![Publier à partir de l’Explorateur de solutions](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Dans la page de publication, cliquez sur **Publier**. Lorsque la nouvelle page web affiche votre liste des tâches, votre application se connecte à la base de données à l’aide de l’identité de service managée.

![Application web Azure après l’activation de Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Vous devriez désormais être en mesure de modifier la liste des tâches comme auparavant.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Accorder des privilèges minimaux à l’identité

Au cours des étapes précédentes, vous avez probablement remarqué que votre identité de service managée était connectée à SQL Server en tant qu’administrateur Azure AD. Pour accorder des privilèges minimaux à votre identité de service managée, vous devez vous connecter au serveur Azure SQL Database en tant qu’administrateur Azure AD, puis ajouter un groupe Azure Active Directory contenant cette identité de service. 

### <a name="add-managed-service-identity-to-an-azure-active-directory-group"></a>Ajouter l’identité de service managée à un groupe Azure Active Directory

Dans le Cloud Shell, ajoutez l’identité de service managée pour votre application dans un nouveau groupe Azure Active Directory appelé _myAzureSQLDBAccessGroup_, comme indiqué dans le script suivant :

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Si vous souhaitez visualiser la sortie JSON complète pour chaque commande, supprimez les paramètres `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Reconfigurer l’administrateur Azure AD

Précédemment, vous avez attribué l’identité de service managée en tant qu’administrateur Azure AD pour votre service SQL Database. Vous ne pouvez pas utiliser cette identité pour une connexion interactive (en vue d’ajouter des utilisateurs de base de données) ; vous devez donc utiliser votre utilisateur Azure AD réel. Pour ajouter votre utilisateur Azure AD, suivez la procédure décrite à la section [Approvisionner un administrateur Azure Active Directory pour votre serveur Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server). 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Accorder des autorisations au groupe Azure Active Directory

Dans le Cloud Shell, connectez-vous à SQL Database en utilisant la commande SQLCMD. Remplacez _\<servername>_ par le nom de votre serveur SQL Database, puis remplacez _\<AADusername>_ et _\<AADpassword>_ par les informations d’identification de votre utilisateur Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

À l’invite SQL, exécutez les commandes ci-après afin d’ajouter le groupe Azure Active Directory que vous avez précédemment créé en tant qu’utilisateur.

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

Tapez `EXIT` pour revenir à l’invite Cloud Shell. Ensuite, réexécutez SQLCMD, mais en spécifiant cette fois le nom de base de données dans _\<dbname>_.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

À l’invite SQL de la base de données souhaitée, exécutez les commandes ci-après pour accorder des autorisations de lecture et d’écriture au groupe Azure Active Directory.

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * activer une identité de service managée ;
> * accorder à l’identité de service un accès à SQL Database ;
> * configurer le code de l’application pour l’authentification auprès de SQL Database à l’aide de l’authentification Azure Active Directory ;
> * accorder des privilèges minimaux à l’identité de service dans SQL Database.

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)
