---
title: 'Tutoriel : comment utiliser Azure Key Vault avec Azure Web App dans .NET | Microsoft Docs'
description: 'Didacticiel : configurer une application ASP.NET Core pour lire un secret dans le coffre de clés'
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
ms.openlocfilehash: 6e30a181bc69678051e6d6469a2f1c533de811df
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686209"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Tutoriel : comment utiliser Azure Key Vault avec Azure Web App dans .NET

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources.

Dans ce tutoriel, vous suivez les étapes nécessaires pour qu’une application web Azure puisse lire les informations depuis Azure Key Vault à l’aide d’identités managées pour les ressources Azure. Ce didacticiel est basé sur [Azure Web Apps](../app-service/app-service-web-overview.md). Vous apprendrez à :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Stockage d’un secret dans le coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Création d’une application web Azure.
> * Activer une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour l’application web.
> * Octroi des autorisations requises à l’application web pour lire des données venant du coffre de clés.
> * Exécuter l’application web sur Azure

Avant d’aller plus loin, lisez les [concepts de base](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prérequis

* Sous Windows :
  * [Kit de développement logiciel (SDK) .NET Core 2.1 ou version ultérieure](https://www.microsoft.com/net/download/windows)

* Sur Mac :
  * Consultez [les nouveautés de Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).

* Toutes les plateformes :
  * Git ([télécharger](https://git-scm.com/downloads)).
  * Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 ou ultérieure. Disponible pour Windows, Mac et Linux.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Qu’est-ce que le service Managed Service Identity et comment fonctionne-t-il ?
Avant d’avancer, il faut comprendre ce qu’est MSI. Azure Key Vault peut stocker des informations d'identification de façon sécurisée afin qu’elles ne se retrouvent pas dans votre code, mais pour les récupérer vous devez vous authentifier sur Azure Key Vault. Pour vous authentifier sur Key Vault, il vous faut des informations d’identification. Problème de démarrage classique. Grâce à la magie d’Azure et Azure AD, MSI offre une « identité de démarrage » qui simplifie grandement le démarrage.

Fonctionnement : Lorsque vous activez MSI pour un service Azure tel que des machines virtuelles, App Service ou Functions, Azure crée un [principal de service](key-vault-whatis.md#basic-concepts) pour l’instance du service dans Azure Active Directory, et injecte les informations d'identification du principal de service dans l’instance du service. 

![MSI](media/MSI.png)

Ensuite, votre code appelle un service de métadonnée local disponible sur les ressources Azure pour obtenir un jeton d'accès.
Il utilise alors le jeton obtenu du point de terminaison local MSI_ENDPOINT pour vous authentifier sur un service Azure Key Vault. 

Commençons maintenant le tutoriel.

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, entrez :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé.
L’exemple suivant crée un groupe de ressources dans l’emplacement USA Ouest :

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Le groupe de ressources que vous venez de créer est utilisé tout au long de cet article.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Ensuite, vous créez un coffre de clés dans le groupe de ressources que vous avez créé à l’étape précédente. Fournissez les informations suivantes :

* Nom de coffre de clés : le nom doit être une chaîne de 3 à 24 caractères et doit contenir uniquement (0-9, a-z, A-Z et -).
* Nom du groupe de ressources.
* Emplacement : **USA Ouest**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver en toute sécurité mais que vous devez rendre disponible à votre application.

Saisissez les commandes suivantes pour créer un secret dans le coffre de clés appelé **AppSecret**. Ce secret stockera la valeur **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Cette commande affiche les informations du secret, y compris l’URI. Une fois ces étapes terminées, vous devez avoir un URI pointant vers un secret dans le coffre de clés. Notez ces informations. Vous en aurez besoin dans une étape ultérieure.

## <a name="create-a-net-core-web-app"></a>Créer une application web .NET Core

Suivez ce [tutoriel](../app-service/app-service-web-get-started-dotnet.md) pour créer une application web .NET Core et la **publier** sur Azure **OU** regardez la vidéo ci-dessous.
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Ouvrir et modifier la solution

1. Accédez à Pages > Fichier About.cshtml.cs.
2. Installer ces 2 packages Nuget
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [Coffre de clés](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importer ce qui suit dans le fichier About.cshtml.cs

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. Votre code dans la classe AboutModel doit ressembler à :
    ```
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

Dans le menu principal de Visual Studio 2017, sélectionnez **Déboguer** > **Démarrer** avec/sans débogage. Lorsque le navigateur s’affiche, accédez à la page **À propos**. La valeur d’**AppSecret** s’affiche.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Activer une identité managée pour l’application web

Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. La [vue d’ensemble des identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) simplifie la résolution de ce problème en fournissant aux services Azure une identité managée automatiquement dans Azure AD (Azure Active Directory). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

1. Revenez à l’interface Azure CLI.
2. Exécutez la commande assign-identity pour créer l’identité de cette application : 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   Veuillez noter que vous devez remplacer <YourAppName> par le nom de l’application publiée sur Azure. Par exemple, si l’application publiée s’appelle MyAwesomeapp.azurewebsites.net, remplacez <YourAppName> par MyAwesomeapp.
 
 La sortie de la commande ci-dessus ressemble à ça. Veuillez prendre note de la valeur PrincipalId lorsque vous publiez l’application sur Azure. Elle doit être au format suivant :
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>La commande dans cette procédure revient à accéder au [portail](https://portal.azure.com) et à affecter au paramètre **Identité / Affecté(e) par le système** la valeur **Activer** dans les propriétés de l’application web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Octroyer des autorisations à votre application pour lire des secrets dans Key Vault
        
Ensuite, exécutez cette commande en utilisant le nom de votre coffre de clés et la valeur de **PrincipalId** :

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Publier l’application web dans Azure

Publiez de nouveau cette application sur Azure pour l’afficher en direct en tant qu’application web, et pour afficher la récupération de la valeur du secret.

1. Dans Visual Studio, sélectionnez le projet **key-vault-dotnet-core-quickstart**.
2. Sélectionnez **Publier** > **Démarrer**.
3. Sélectionnez **Créer**.

Dans la commande ci-dessus, vous donnez l’identité Identity(MSI) des autorisations App Service pour effectuer des opérations **get** et **list** sur votre coffre de clés. <br />
Maintenant lorsque vous exécutez l’application, vous devriez voir votre valeur secrète récupérée. 

Et voilà, c’est terminé. Vous avez créé une application web dans .NET qui stocke et récupère ses secrets de coffre de clés.
