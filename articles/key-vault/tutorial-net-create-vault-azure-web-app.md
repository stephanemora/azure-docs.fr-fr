---
title: Tutoriel - Utiliser Azure Key Vault avec une application web Azure dans .NET | Microsoft Docs
description: Tutoriel - Configurer une application ASP.NET Core pour lire un secret dans le coffre de clés
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 50a7f3166d677fe1af961866ccae4445a3d810b8
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322139"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Tutoriel : Utiliser Azure Key Vault avec une application web Azure dans .NET

Azure Key Vault vous permet de protéger les secrets tels que les clés API et les chaînes de connexion de base de données. Il vous permet d’accéder à vos applications, services et ressources informatiques.

Dans ce tutoriel, vous allez apprendre à créer une application web Azure qui peut lire des informations dans un coffre de clés Azure. Le processus utilise des identités managées pour les ressources Azure. Pour plus d’informations sur les applications web Azure, consultez [Azure Web Apps](../app-service/app-service-web-overview.md).

Cet article vous montre comment :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Stockage d’un secret dans le coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Création d’une application web Azure.
> * Activer une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour l’application web.
> * Octroi des autorisations requises à l’application web pour lire des données venant du coffre de clés.
> * Exécuter l’application web dans Azure

Avant de continuer, lisez [Concepts de base de Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prérequis

* Sous Windows :
  * [Kit de développement logiciel (SDK) .NET Core 2.1 ou version ultérieure](https://www.microsoft.com/net/download/windows)

* Sur Mac :
  * [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/)

* Toutes les plateformes :
  * [Git](https://git-scm.com/downloads)
  * Abonnement Azure <br />Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 ou ultérieure, disponible pour Windows, Mac et Linux
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>Fonctionnement de Managed Service Identity

Azure Key Vault stocke les informations d’identification de manière sécurisée, en dehors de votre code. Toutefois, vous devez vous authentifier auprès d’Azure Key Vault pour récupérer vos clés. Pour vous authentifier auprès de Key Vault, vous avez besoin d’informations d’identification. Il s’agit d’un dilemme de démarrage classique. Managed Service Identity (MSI) résout ce problème en fournissant une _identité de démarrage_.

Lorsque vous activez MSI pour un service Azure (par exemple : Machines Virtuelles, App Service ou Functions), Azure crée un [principal de service](key-vault-whatis.md#basic-concepts). MSI procède ainsi pour l’instance du service dans Azure Active Directory (Azure AD) et injecte les informations d’identification du principal de service dans cette instance.

![Diagramme MSI](media/MSI.png)

Ensuite, votre code appelle un service de métadonnée local disponible dans la ressource Azure pour obtenir un jeton d’accès. Il utilise alors le jeton obtenu du point de terminaison local MSI_ENDPOINT pour vous authentifier sur un service Azure Key Vault.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI Azure, entrez ceci :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

1. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create).
1. Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé. L’exemple suivant crée un groupe de ressources dans l’emplacement USA Ouest :

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Vous allez utiliser ce groupe de ressources tout au long du tutoriel.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Pour créer un coffre de clés dans votre groupe de ressources, fournissez les informations suivantes :

* Nom du coffre de clés : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres, des lettres et des traits d’union (par exemple : 0-9, a-z, A-Z et -)
* Nom de groupe ressources
* Localisation : **USA Ouest**

Dans Azure CLI, entrez la commande suivante :

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Vous pouvez maintenant ajouter un secret. Il peut s’agir d’une chaîne de connexion SQL ou de toute autre information que vous devez sécuriser et garder à disposition pour votre application.

Tapez la commande suivante pour créer un secret dans le coffre de clés appelé **AppSecret**. Ce secret stocke la valeur **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pour voir la valeur contenue dans le secret sous forme de texte brut, entrez la commande suivante :

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Cette commande affiche les informations du secret, y compris l’URI. Une fois ces étapes terminées, vous devez avoir un URI pointant vers un secret dans le coffre de clés. Notez ces informations. Vous en aurez besoin dans une étape ultérieure.

## <a name="create-a-net-core-web-app"></a>Créer une application web .NET Core

Suivez ce [tutoriel](../app-service/app-service-web-get-started-dotnet.md) pour créer une application web .NET Core et la **publier** dans Azure. Vous pouvez également regarder la vidéo suivante :

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Ouvrir et modifier la solution

1. Accédez au fichier **Pages** > **About.cshtml.cs**.
2. Installez ces packages NuGet :
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [Coffre de clés](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importez le code suivant dans le fichier About.cshtml.cs :

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. Le code de la classe AboutModel doit ressembler à ceci :

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
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>Exécution de l'application

1. Dans le menu principal de Visual Studio 2017, sélectionnez **Déboguer** > **Démarrer** avec ou sans débogage. 
1. Lorsque le navigateur s’affiche, accédez à la page **À propos**.
1. La valeur d’**AppSecret** s’affiche.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Activer une identité managée pour l’application web

Azure Key Vault permet de stocker des informations d’identification et autres secrets de manière sécurisée. Toutefois, votre code doit s’authentifier auprès de Key Vault pour les récupérer. La [vue d’ensemble des identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) vous aide à résoudre ce problème en fournissant automatiquement aux services Azure une identité managée dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

1. Dans Azure CLI, exécutez la commande assign-identity pour créer l’identité de cette application :

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >Vous devez remplacer \<YourAppName\> par le nom de l’application publiée dans Azure. Par exemple, si le nom de votre application publiée était **MyAwesomeapp.azurewebsites.net**, remplacez \<YourAppName\> par **MyAwesomeapp**.

1. Prenez note de `PrincipalId` quand vous publiez l’application dans Azure. À l’étape 1, la sortie de la commande doit être au format suivant :

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>La commande dans cette procédure revient à accéder au [portail](https://portal.azure.com) et à affecter au paramètre **Identité / Affecté(e) par le système** la valeur **Activer** dans les propriétés de l’application web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Octroyer des autorisations à votre application pour lire des secrets dans Key Vault

Remplacez \<YourKeyVaultName\> par le nom de votre coffre de clés et \<PrincipalId\> par la valeur de **PrincipalId** dans la commande suivante :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Cette commande fournit l’identité MSI des autorisations App Service pour effectuer des opérations **get** et **list** sur votre coffre de clés.

## <a name="publish-the-web-application-to-azure"></a>Publier l’application web dans Azure

Republiez votre application web dans Azure pour voir si votre application web en production peut récupérer la valeur du secret.

1. Dans Visual Studio, sélectionnez le projet **key-vault-dotnet-core-quickstart**.
2. Sélectionnez **Publier** > **Démarrer**.
3. Sélectionnez **Créer**.

Lorsque vous exécutez l’application, vous voyez normalement qu’elle peut récupérer votre valeur de secret.

Vous avez créé une application web dans .NET qui stocke et récupère ses secrets à partir du coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Guide du développeur de coffre de clés Azure](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-developers-guide)
