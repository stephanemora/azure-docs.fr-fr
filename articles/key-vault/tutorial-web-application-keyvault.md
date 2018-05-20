---
title: Configurer une application web Azure pour lire un secret dans le coffre de clés | Microsoft Docs
description: Didacticiel Configurer une application ASP.NET Core pour lire un secret dans le coffre de clés
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: b4e317a82b93513c6161d9da0c55883e99580cbb
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Didacticiel : Configurer une application web Azure pour lire un secret dans le coffre de clés

Dans ce didacticiel, vous passez en revue les étapes nécessaires pour qu’une application web Azure puisse lire les informations dans le coffre de clés à l’aide d’identités de service managées. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés.
> * Stockage d’un secret dans le coffre de clés.
> * Création d’une application web Azure.
> * Activer les identités de service managées
> * Accordez les autorisations requises à l’application pour lire des données dans le coffre de clés.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel exige que vous exécutiez Azure CLI version 2.0.4 ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

Pour vous connecter à Azure à l’aide de l’interface CLI, vous pouvez taper :

```azurecli
az login
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

Le groupe de ressources que vous venez de créer est utilisé tout au long de ce didacticiel.

## <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Ensuite, vous créez un coffre de clés dans le groupe de ressources créé à l’étape précédente. Vous devez fournir certaines informations :

>[!NOTE]
> Bien que « ContosoKeyVault » soit utilisé comme le nom de notre coffre de clés dans ce didacticiel, vous devez utiliser un nom unique.

* Nom du coffre **ContosoKeyVault**.
* Nom du groupe de ressources **ContosoResourceGroup**.
* Emplacement **Est des États-Unis**.

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

La sortie de cette commande affiche les propriétés du coffre de clés que vous venez de créer. Notez les deux propriétés ci-dessous :

* **Nom du coffre** : dans l’exemple, il s’agit de **ContosoKeyVault**. Vous allez utiliser le nom de votre coffre de clés pour toutes les commandes de coffre de clés.
* **URI du coffre** : dans l’exemple, il s’agit de https://<YourKeyVaultName>.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

>[!IMPORTANT]
> Si vous obtenez l’erreur Le paramètre « vault_name » doit être conforme au modèle suivant : ^[a-zA-Z0-9-]{3,24}$. La valeur du paramètre -name n’était pas unique ou conforme à une chaîne composée de 3 à 24 caractères alphanumériques.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret dans le coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver en toute sécurité mais que vous devez rendre disponible à votre application. Dans ce didacticiel, le mot de passe sera appelé **AppSecret** et y stocke la valeur de **MySecret**.

Tapez les commandes ci-dessous pour créer un secret dans Key Vault, appelé **AppSecret** et qui va stocker la valeur **MySecret** :

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

Cette commande affiche les informations du secret, y compris l’URI. Après ces étapes, vous devez avoir un URI pointant vers un secret dans Azure Key Vault. Notez ces informations. Vous en aurez besoin dans une étape ultérieure.

## <a name="create-a-web-app"></a>Créer une application web

Dans cette section, vous créez une application MVC ASP.NET que vous déployez dans Azure sous la forme d’une application web. Pour plus d'informations sur Azure Web Apps, consultez [Vue d'ensemble de Web Apps](../app-service/app-service-web-overview.md).

1. Dans Visual Studio, créez un projet en sélectionnant **Fichier > Nouveau > Projet**. 

2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C# > Web > Application web ASP.NET Core**.

3. Nommez l’application **WebKeyVault**, puis sélectionnez **OK**.
   >[!IMPORTANT]
   > Vous devez nommer l’application WebKeyVault pour que le code que vous copiez et collez corresponde à l’espace de noms. Si vous avez donné un autre nom au site, vous devrez modifier le code pour qu’il corresponde au nom du site.

    ![Boîte de dialogue New ASP.NET Project](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Vous pouvez déployer n’importe quel type d’application web ASP.NET Core dans Azure. Dans ce didacticiel, sélectionnez le modèle **Application web** et vérifiez que l’authentification est définie sur **Aucune authentification**.

    ![Aucune boîte de dialogue d’authentification ASPNET](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Sélectionnez **OK**.

6. Une fois le projet ASP.NET Core créé, la page d’accueil ASP.NET Core s’affiche et fournit de nombreux liens vers des ressources pour vous aider à commencer.

7. Dans le menu, sélectionnez **Déboguer > Exécuter sans débogage** pour exécuter l’application web localement.

## <a name="modify-the-web-app"></a>Modifier l’application web

Deux packages NuGet doivent être installés pour votre application web. Pour les installer, suivez les étapes ci-dessous :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit de la souris sur le nom de votre site web.
2. Sélectionnez **Gérer les packages NuGet pour la solution...**.
3. Cochez la case en regard de la zone de recherche. **Inclure la version préliminaire**
4. Recherchez les deux packages NuGet répertoriés ci-dessous et acceptez qu’ils soient ajoutés à votre solution :

    * [Microsoft.Azure.Services.AppAuthentication (préversion)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) : facilite la récupération de jetons d’accès dans les scénarios d’authentification de Service-à-Service-Azure. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) : contient les méthodes permettant d’interagir avec Azure Key Vault.

5. Utilisez l’Explorateur de solutions pour ouvrir `Program.cs` et remplacez le contenu du fichier Program.cs par le code suivant. Remplacez ```<YourKeyVaultName>``` par le nom de votre coffre de clés :

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
            public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Utilisez l’Explorateur de solutions pour accéder à la section **Pages** et ouvrez `About.cshtml`. Remplacez le contenu du fichier **About.cshtml.cs** par le code suivant :

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. Dans le menu principal, choisissez **Déboguer** > **Exécuter sans débogage**. Lorsque le navigateur s’affiche, accédez à la page **À propos**. La valeur d’AppSecret s’affiche.

>[!IMPORTANT]
> Si vous obtenez un message Erreur HTTP 502.5 - Échec du processus, vérifiez le nom du coffre de clés spécifié dans `Program.cs`.

## <a name="publish-the-web-application-to-azure"></a>Publier l’application web dans Azure

1. Au-dessus de l’éditeur, sélectionnez **WebKeyVault**.
2. Sélectionnez **Publier**.
3. Sélectionnez **Publier** à nouveau.
4. Sélectionnez **Créer**.

>[!IMPORTANT]
> Une fenêtre de navigateur s’ouvre avec un message 502.5 - Échec du processus. Ceci est normal. Vous devrez accorder à l’application des droits d’identité pour lire des secrets dans Azure Key Vault.

## <a name="enable-managed-service-identity"></a>Activer Managed Service Identity

Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. L’identité du service administré (MSI) simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement gérée dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

1. Revenir à l’interface Azure CLI
2. Exécutez la commande assign-identity pour créer l’identité de cette application :

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>Cela revient à accéder au portail et à régler **Managed Service Identity** sur **Activer** dans les propriétés de l’application web.

## <a name="grant-rights-to-the-application-identity"></a>Accorder des droits à l’identité de l’application

À l’aide du portail Azure, accédez aux stratégies d’accès du coffre de clés et accordez-vous l’accès Gestion des secrets dans Azure Key Vault. Cela vous permettra d’exécuter l’application sur votre ordinateur de développement local.

1. Recherchez votre coffre de clés dans la boîte de dialogue **Rechercher des ressources** dans le portail Azure.
2. Sélectionnez **Stratégies d’accès**.
3. Sélectionnez **Nouveau**, puis dans la section **Autorisations du secret**, sélectionnez **Affichage** et **Liste**.
4. Sélectionnez **Sélectionner le principal** et ajoutez l’identité de l’application. Elle aura le même nom que l’application.
5. Choisissez **OK**.

Maintenant votre compte dans Azure et l’identité de l’application ont le droit de lire des informations dans Azure Key Vault. Si vous actualisez la page, la page d’accueil du site doit s’afficher. Si vous sélectionnez **À propos**, vous voyez la valeur que vous avez stockée dans Azure Key Vault.

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer un groupe de ressources et toutes ses ressources, utilisez la commande **az group delete**.

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide du développeur de coffre de clés Azure](key-vault-developers-guide.md)
