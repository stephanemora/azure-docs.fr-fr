---
title: Tutoriel - Utiliser Azure Key Vault avec une application web Azure dans .NET | Microsoft Docs
description: Dans ce tutoriel, vous allez configurer une application ASP.NET Core pour lire un secret dans votre coffre de clés.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: e636ab843a9801097bf770ca12c9d1e512750c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198114"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Tutoriel : Utiliser Azure Key Vault avec une application web Azure dans .NET

Azure Key Vault vous permet de protéger les secrets tels que les clés API et les chaînes de connexion de base de données. Il vous permet d’accéder à vos applications, services et ressources informatiques.

Dans ce tutoriel, vous allez apprendre à créer une application web Azure qui peut lire des informations dans un coffre de clés Azure. Le processus utilise des identités managées pour les ressources Azure. Pour plus d’informations sur les applications web Azure, consultez [Azure App Service](../app-service/overview.md).

Ce didacticiel explique les procédures suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Ajoutez un secret au coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Créez une application web Azure.
> * Activer une identité managée pour l’application web.
> * Affectez une autorisation à l’application web.
> * Exécutez l’application web sur Azure.

Avant de continuer, lisez les [concepts de base de Key Vault](basic-concepts.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prérequis

* Pour Windows  : [SDK .NET Core 2.1 ou version ultérieure](https://www.microsoft.com/net/download/windows)
* Pour Mac : [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/)
* Pour Windows, Mac et Linux :
  * [Git](https://git-scm.com/downloads)
  * Ce didacticiel nécessite que vous exécutiez l’interface Azure CLI localement. Vous devez avoir installé Azure CLI 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>À propos de Managed Service Identity (MSI)

Azure Key Vault stocke les informations d’identification de manière sécurisée, de façon à ce qu’elles n’apparaissent pas dans votre code. Toutefois, vous devez vous authentifier auprès d’Azure Key Vault pour récupérer vos clés. Pour vous authentifier auprès de Key Vault, vous avez besoin d’informations d’identification. Il s’agit d’un dilemme de démarrage classique. Managed Service Identity (MSI) résout ce problème en fournissant une _identité de démarrage_.

Lorsque vous activez MSI pour un service Azure, par exemple, Machines virtuelles Azure, Azure App Service ou Azure Functions, Azure crée un [principal de service](basic-concepts.md). MSI procède ainsi pour l’instance du service dans Azure Active Directory (Azure AD) et injecte les informations d’identification du principal de service dans cette instance.

![Diagramme MSI](media/MSI.png)

Ensuite, pour obtenir un jeton d’accès, votre code appelle un service de métadonnées local disponible dans la ressource Azure. Votre code utilise alors le jeton qu’il obtient du point de terminaison local MSI_ENDPOINT pour s’authentifier auprès d’un service Azure Key Vault.

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, entrez :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create).

Sélectionnez ensuite un nom de groupe de ressources et renseignez l’espace réservé. L’exemple suivant crée un groupe de ressources dans l’emplacement USA Ouest :

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Vous allez utiliser ce groupe de ressources tout au long du tutoriel.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Pour créer un coffre de clés dans votre groupe de ressources, fournissez les informations suivantes :

* Nom du coffre de clés : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres (0-9), des lettres (a-z, A-Z) et des traits d’union (-).
* Nom de groupe ressources
* Localisation : **USA Ouest**

Dans Azure CLI, entrez la commande suivante :

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Vous pouvez maintenant ajouter un secret. Il peut s’agir d’une chaîne de connexion SQL ou de toute autre information que vous devez sécuriser et garder à disposition pour votre application.

Pour créer un secret dans le coffre de clés appelé **AppSecret**, tapez la commande suivante : 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ce secret stocke la valeur **MySecret**.

Pour voir la valeur contenue dans le secret sous forme de texte brut, entrez la commande suivante :

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Cette commande affiche les informations du secret, y compris l’URI. 

Une fois ces étapes terminées, vous devez avoir un URI pointant vers un secret dans le coffre de clés. Notez ces informations pour pouvoir les utiliser plus tard dans ce didacticiel. 

## <a name="create-a-net-core-web-app"></a>Créer une application web .NET Core

Pour créer une application web .NET Core et la publier sur Azure, suivez les instructions de la section [créer une application web ASP.NET Core dans Azure](../app-service/app-service-web-get-started-dotnet.md). 

Vous pouvez également regarder cette vidéo :

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Ouvrir et modifier la solution

1. Accédez au fichier **Pages** > **About.cshtml.cs**.

1. Installez ces packages NuGet :
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [Coffre de clés](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importez le code suivant dans le fichier *About.cshtml.cs* :

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Votre code dans la classe AboutModel doit ressembler à ceci :

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Exécuter l’application web

1. Dans le menu principal de Visual Studio 2019, sélectionnez **Déboguer** > **Démarrer**, avec ou sans débogage. 
1. Dans le navigateur, accédez à la page **À propos de**.  
    La valeur d’**AppSecret** s’affiche.

## <a name="enable-a-managed-identity"></a>Activer une identité managée

Azure Key Vault permet de stocker des informations d’identification et autres secrets de manière sécurisée. Toutefois, votre code doit s’authentifier auprès de Key Vault pour les récupérer. La [vue d’ensemble des identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) vous aide à résoudre ce problème en fournissant automatiquement aux services Azure une identité managée dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir à afficher les informations d’identification dans votre code.

Dans Azure CLI, pour créer l’identité de cette application, exécutez la commande assign-identity :

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Remplacez \<YourAppName> par le nom de l’application publiée dans Azure.  
    Par exemple, si le nom de votre application publiée était **MyAwesomeapp.azurewebsites.net**, remplacez \<YourAppName> par **MyAwesomeapp**.

Prenez note de `PrincipalId` quand vous publiez l’application dans Azure. À l’étape 1, la sortie de la commande doit être au format suivant :

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>La commande dans cette procédure revient à accéder au [portail Azure](https://portal.azure.com) et à affecter au paramètre **Identité/Affecté(e) par le système** la valeur **Activer** dans les propriétés de l’application web.

## <a name="assign-permissions-to-your-app"></a>Accorder des autorisations à votre application

Remplacez \<YourKeyVaultName> par le nom de votre coffre de clés et \<PrincipalId> par la valeur de **PrincipalId** dans la commande suivante :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Cette commande fournit l’identité MSI de l’autorisation App Service pour effectuer des opérations **get** et **list** sur votre coffre de clés.

## <a name="publish-the-web-app-to-azure"></a>Publier l’application web dans Azure

Republiez votre application web dans Azure pour vérifier si votre application web en production peut récupérer la valeur du secret.

1. Dans Visual Studio, sélectionnez le projet **key-vault-dotnet-core-quickstart**.
2. Sélectionnez **Publier** > **Démarrer**.
3. Sélectionnez **Create** (Créer).

Lorsque vous exécutez l’application, vous voyez normalement qu’elle peut récupérer votre valeur de secret.

Vous avez créé une application web dans .NET qui stocke et récupère ses secrets à partir de votre coffre de clés.

## <a name="clean-up-resources"></a>Nettoyer les ressources
Quand ils ne sont plus nécessaires, vous pouvez supprimer la machine virtuelle et le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Guide du développeur de coffre de clés Azure](key-vault-developers-guide.md)
