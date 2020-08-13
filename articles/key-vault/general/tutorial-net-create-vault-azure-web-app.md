---
title: Tutoriel - Utiliser Azure Key Vault avec une application web Azure dans .NET | Microsoft Docs
description: Dans ce tutoriel, vous allez configurer une application ASP.NET Core pour lire un secret dans votre coffre de clés.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: b957ea9131c5124925b74576fd78665522afd8dc
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080230"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Tutoriel : Utiliser une identité managée pour connecter Key Vault à une application web Azure avec .NET

Azure Key Vault permet de stocker des informations d’identification et autres secrets de manière sécurisée. Toutefois, votre code doit s’authentifier auprès de Key Vault pour les récupérer. La [vue d’ensemble des identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md) vous aide à résoudre ce problème en fournissant automatiquement aux services Azure une identité managée dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir à afficher les informations d’identification dans votre code.

Ce tutoriel utilise une identité managée pour authentifier une application web Azure avec un Azure Key Vault. Bien que les étapes utilisent la [bibliothèque cliente Azure Key Vault v4 pour .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) et [Azure CLI](/cli/azure/get-started-with-azure-cli), les mêmes principes de base s’appliquent lorsque vous utilisez le langage de développement de votre choix, Azure PowerShell et/ou le portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Kit SDK .NET Core 3.1 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources pour héberger votre coffre de clés et votre application web à l’aide de la commande [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Configurer votre coffre de clés

Vous allez maintenant créer un coffre de clés et y placer un secret, pour une utilisation ultérieure dans ce didacticiel.

Utilisez la commande [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) pour créer un coffre de clés :

> [!Important]
> Chaque coffre de clés doit avoir un nom unique. Remplacez <your-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Notez la valeur `vaultUri` retournée, qui est au format « https://&lt;nom_de_votre_coffre_de_clés&gt;.vault.azure.net/ ». Elle sera utilisée à l’étape [Mettre à jour le code](#update-the-code).

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-net-web-app"></a>Créer une application web .NET

### <a name="create-a-local-app"></a>Créer une application locale

Dans une fenêtre de terminal sur votre machine, créez un répertoire nommé `akvwebapp` et remplacez le répertoire actuel par ce dernier.

```bash
mkdir akvwebapp
cd akvwebapp
```

À présent, créez une application .NET Core avec la commande [dotnet new web](/dotnet/core/tools/dotnet-new) :

```bash
dotnet new web
```

Exécutez l’application localement pour voir à quoi elle devrait ressembler lorsque vous la déploierez sur Azure. 

```bash
dotnet run
```

Ouvrez un navigateur web et accédez à l’application à l’adresse `http://localhost:5000`.

Vous voyez apparaître sur la page le message **Hello World** de l’exemple d’application.

### <a name="initialize-the-git-repository"></a>Initialiser le dépôt Git

Dans la fenêtre de terminal, appuyez sur **Ctrl + C** pour quitter le serveur web.  Initialisez un dépôt Git pour le projet .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

Vous pouvez déployer le protocole FTP et Git local sur une application web Azure en faisant appel à un *utilisateur de déploiement*. Une fois que vous avez créé votre utilisateur de déploiement, vous pouvez l’utiliser pour tous vos déploiements Azure. Votre nom d’utilisateur et votre mot de passe de déploiement au niveau du compte sont différents de vos informations d’identification de l’abonnement Azure. 

Pour configurer l’utilisateur de déploiement, exécutez la commande [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Choisissez un nom d’utilisateur et un mot de passe conformes à ces instructions : 

- Le nom d’utilisateur doit être unique dans Azure et, pour les push Git locaux, ne doit pas contenir le symbole « @ ». 
- Le mot de passe doit comporter au moins huit caractères et inclure deux des trois éléments suivants : lettres, chiffres et symboles. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

La sortie JSON affiche le mot de passe comme étant `null`. Si vous obtenez une erreur `'Conflict'. Details: 409`, modifiez le nom d’utilisateur. Si vous obtenez une erreur `'Bad Request'. Details: 400`, utilisez un mot de passe plus fort. 

Enregistrez le nom d’utilisateur et le mot de passe à utiliser pour déployer vos applications web.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest) d’Azure CLI. Cet exemple crée un plan App Service nommé `myAppServicePlan` dans le niveau tarifaire **Gratuit** :

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>Créer une application web distante

Créez une [application web Azure](../../app-service/overview.md#app-service-on-linux) dans le plan App Service `myAppServicePlan`. 

> [!Important]
> Comme pour Key Vault, une application web Azure doit avoir un nom unique. Remplacez \<your-webapp-name\> par le nom de votre application web comme dans les exemples suivants.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.

Accédez à l’application que vous venez de créer. Remplacez _&lt;your-webapp-name>_ par le nom de votre application.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Vous verrez la page web par défaut d’une application web Azure nouvellement créée.

### <a name="deploy-your-local-app"></a>Déployer votre application locale

De retour dans la fenêtre du terminal local, ajoutez une instance Azure distante à votre dépôt Git local en remplaçant *\<deploymentLocalGitUrl-from-create-step>* par l’URL du dépôt Git distant que vous avez enregistrée à l’étape [Créer une application web distante](#create-a-remote-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Lorsque le gestionnaire d’informations d’identification de Git vous invite à entrer des informations d’identification, utilisez les informations que vous avez créées à l’étape [Configurer un utilisateur de déploiement](#configure-a-deployment-user).

```bash
git push azure master
```

L’exécution de cette commande peut prendre quelques minutes. Pendant son exécution, des informations semblables à ce qui suit s’affichent :
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Accédez à (ou actualisez) l’application déployée à l’aide de votre navigateur web.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Vous voyez apparaître sur la page le message « Hello World ! » que vous avez vu précédemment lorsque vous avez visité `http://localhost:5000`.

## <a name="create-and-assign-a-managed-identity"></a>Créer et attribuer une identité gérée

Dans Azure CLI, pour créer l’identité de cette application, exécutez la commande [az webapp-identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) :

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

L’opération renverra cet extrait de code JSON :

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Pour permettre à votre application web d’effectuer des opérations **get** et **list** sur votre coffre de clés, transmettez le principalID à la command e Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) :

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Modifier l’application pour accéder à votre coffre de clés

### <a name="install-the-packages"></a>Installer les packages

À partir de la fenêtre du terminal, installez la bibliothèque de client Azure Key Vault pour les packages .NET :

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>Mettez à jour le code

Recherchez et ouvrez le fichier Startup.cs dans votre projet akvwebapp. 

Ajoutez ces deux lignes à l’en-tête :

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Ajoutez ces lignes avant l’appel de `app.UseEndpoints`, en mettant à jour l’URI pour refléter la valeur `vaultUri` de votre coffre de clés. Le code ci-dessous utilise ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) pour l’authentification auprès du coffre de clés, en utilisant le jeton de l’identité managée par l’application pour s’authentifier. Il utilise également l’interruption exponentielle pour les nouvelles tentatives en cas de limitation du coffre de clés.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

Mettez à jour la ligne `await context.Response.WriteAsync("Hello World!");` avec ce qui suit :

```csharp
await context.Response.WriteAsync(secretValue);
```

Veillez à enregistrer vos modifications avant de passer à l’étape suivante.

### <a name="redeploy-your-web-app"></a>Redéployez votre application web

Après avoir mis à jour votre code, vous pouvez le redéployer sur Azure avec les commandes Git suivantes :

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Accédez à votre application web terminée

```bash
http://<your-webapp-name>.azurewebsites.net
```

Là où **Hello World** s’affichait, vous devez maintenant voir la valeur de votre secret affichée : **Vous avez réussi !**

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- En savoir plus sur les [identités managées pour App Service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Consultez la [référence de l’API de la bibliothèque de client Azure Key Vault pour .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)
- Consultez le [code source de la bibliothèque de client Azure Key Vault pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)
- Consultez le [package NuGet de la bibliothèque de client Azure Key Vault v4 pour .NET](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)


