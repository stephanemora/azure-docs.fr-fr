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
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481014"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutoriel : Sécuriser la connexion Azure SQL Database à partir d’App Service à l’aide d’une identité managée

[App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques dans Azure. Il offre également une [identité managée](overview-managed-identity.md) pour votre application, qui constitue une solution clé en main permettant de sécuriser l’accès à [Azure SQL Database](/azure/sql-database/) et à d’autres services Azure. Les identités managées dans App Service sécurisent votre application en en éliminant les secrets, par exemple les informations d’identification dans les chaînes de connexion. Dans ce tutoriel, vous allez ajouter l’identité managée à l’exemple d’application web ASP.NET que vous avez créé dans [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Lorsque vous aurez terminé, votre exemple d’application se connectera à SQL Database en toute sécurité sans nécessiter aucun nom d’utilisateur ni mot de passe.

> [!NOTE]
> Ce scénario est actuellement pris en charge par .NET Framework 4.7.2 et les versions ultérieures. [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) prend en charge le scénario, mais il n’est pas encore inclus dans les images par défaut dans App Service. 
>

Vous apprendrez à :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * Configurer Entity Framework pour utiliser l’authentification Azure AD avec SQL Database
> * Se connecter à SQL Database à partir de Visual Studio à l’aide de l’authentification Azure AD

> [!NOTE]
>L’authentification Azure AD est _différente_ de [l’authentification Windows intégrée](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) dans Active Directory (AD DS) local. AD DS et Azure AD utilisent des protocoles d’authentification totalement différents. Pour plus d’informations, consultez la [documentation sur Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Cet article se poursuit là où vous l'avez laissé dans [Tutoriel : Créer une application ASP.NET dans Azure avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Si vous ne l’avez pas encore fait, commencez par suivre ce didacticiel. Vous pouvez également adapter la procédure à votre propre application ASP.NET avec SQL Database.

Pour déboguer votre application à l’aide de SQL Database en tant que back-end, assurez-vous que vous avez [autorisé la connexion cliente à partir de votre ordinateur](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Accorder à l’utilisateur Azure AD l’accès à la base de données

Tout d’abord, activez l’authentification Azure AD à SQL Database en attribuant un utilisateur Azure AD en tant qu’administrateur Active Directory du serveur SQL Database. Cet utilisateur est différent du compte Microsoft que vous avez utilisé pour vous inscrire à votre abonnement Azure. Il doit s’agir d’un utilisateur créé, importé, synchronisé ou invité à Azure AD. Pour plus d’informations sur les utilisateurs Azure AD autorisés, consultez [Fonctionnalités Azure AD et limitations dans SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Recherchez l’ID d’objet de l’utilisateur Azure AD en utilisant [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) et remplacez *\<user-principal-name>* par votre propre valeur. Le résultat est enregistré dans une variable.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Pour afficher la liste de tous les noms d’utilisateur dans Azure AD, exécutez `az ad user list --query [].userPrincipalName`.
>

Ajoutez cet utilisateur Azure AD en tant qu’administrateur Active Directory à l’aide de la commande [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) dans Cloud Shell. Dans la commande ci-après, remplacez *\<server-name>* par votre propre valeur.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Pour plus d’informations sur l’ajout d’un administrateur Active Directory, consultez [Provisionner un administrateur Azure Active Directory pour votre serveur Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configuration de Visual Studio

Pour activer le développement et le débogage dans Visual Studio, ajoutez votre utilisateur Azure AD dans Visual Studio en sélectionnant **Fichier** > **Paramètres du compte** à partir du menu, puis cliquez sur **Ajouter un compte**.

Pour définir l’utilisateur Azure AD pour l’authentification de service Azure, sélectionnez **Outils** > **Options** dans le menu, puis sélectionnez **Authentification du service Azure** > **Sélection du compte**. Sélectionnez l’utilisateur Azure AD que vous avez ajouté, puis cliquez sur **OK**.

Vous êtes maintenant prêt à développer et déboguer votre application avec SQL Database en tant que back-end, à l’aide de l’authentification Azure AD.

## <a name="modify-aspnet-project"></a>Modifier un projet ASP.NET

Dans Visual Studio, ouvrez la Console du gestionnaire de package et ajoutez le package NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) :

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

Dans *Web.config*, depuis le début du fichier, apportez les modifications suivantes :

- Dans `<configSections>`, ajoutez la déclaration de section suivante :

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Sous la balise `</configSections>` de fermeture, ajoutez le code XML suivant pour `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Recherchez la chaîne de connexion appelée `MyDbConnection` et remplacez sa valeur `connectionString` par `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Remplacez _\<server-name>_ et _\<db-name>_ par le nom de votre serveur et le nom de votre base de données.

Entrez `Ctrl+F5` pour réexécuter l’application. La même application CRUD dans votre navigateur se connecte maintenant à Azure SQL Database directement, à l’aide de l’authentification Azure AD. Cette configuration vous permet d’exécuter des migrations de base de données. Par la suite, lorsque vous déployez vos modifications vers App Service, les mêmes paramètres fonctionnent avec l’identité managée de l’application.

## <a name="use-managed-identity-connectivity"></a>Utiliser la connectivité de l’identité managée

Ensuite, configurez votre application App Service pour qu’elle se connecte à SQL Database avec une identité managée attribuée par le système.

### <a name="enable-managed-identity-on-app"></a>Activer une identité managée sur l’application

Pour activer une identité managée pour votre application Azure, utilisez la commande [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) dans Cloud Shell. Dans la commande ci-après, remplacez *\<app-name>* par votre propre valeur.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Voici un exemple de sortie :

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Ajouter l’identité managée à un groupe Azure AD

Pour accorder cet accès d’identité à votre base de données SQL Database, vous devez l’ajouter à un [groupe Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). Dans Cloud Shell, ajoutez l’identité managée dans un nouveau groupe nommé _myAzureSQLDBAccessGroup_, comme dans le script suivant :

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Si vous souhaitez visualiser la sortie JSON complète pour chaque commande, supprimez les paramètres `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Accorder des autorisations à un groupe Azure AD

Dans le Cloud Shell, connectez-vous à SQL Database en utilisant la commande SQLCMD. Remplacez _\<server-name>_ par le nom de votre serveur SQL Database, puis _\<dbname>_ par le nom de base de données utilisé par votre application, puis _\<aad-user-name>_ et _\<aad-password>_ par les informations d’identification de votre utilisateur Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

À l’invite SQL de la base de données souhaitée, exécutez les commandes suivantes pour ajouter le groupe Azure AD et accorder les autorisations dont votre application a besoin. Par exemple, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Tapez `EXIT` pour revenir à l’invite Cloud Shell.

### <a name="modify-connection-string"></a>Modifier la chaîne de connexion

Rappelez-vous que les modifications apportées dans `Web.config` fonctionnent avec l’identité managée. Par conséquent, la seule chose à faire consiste à supprimer la chaîne de connexion existante de votre application, que Visual Studio a créée lors du premier déploiement de votre application. Utilisez la commande suivante en remplaçant *\<app-name>* par le nom de votre application.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publier vos modifications

Il ne vous reste plus qu’à publier vos modifications sur Azure.

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **DotNetAppSqlDb**, puis sélectionnez **Publier**.

![Publier à partir de l’Explorateur de solutions](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Dans la page de publication, cliquez sur **Publier**. Lorsque la nouvelle page web affiche votre liste des tâches, votre application se connecte à la base de données à l’aide de l’identité managée.

![Application Azure après l’activation des Migrations Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Vous devriez désormais être en mesure de modifier la liste des tâches comme auparavant.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les identités managées
> * Accorder à l’identité managée un accès à SQL Database
> * Configurer Entity Framework pour utiliser l’authentification Azure AD avec SQL Database
> * Se connecter à SQL Database à partir de Visual Studio à l’aide de l’authentification Azure AD

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à Azure App Service](app-service-web-tutorial-custom-domain.md)
