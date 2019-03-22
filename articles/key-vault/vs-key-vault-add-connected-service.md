---
title: Ajouter la prise en charge de Key Vault à votre projet ASP.NET à l’aide de Visual Studio - Azure Key Vault | Microsoft Docs
description: Ce tutoriel vous montre comment ajouter la prise en charge de Key Vault à une application web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: d95bd114be712953b79ef5afbb0915173f6de26c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339276"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Ajouter Key Vault à votre application web à l’aide des Services connectés Visual Studio

Dans ce tutoriel, vous allez voir comment ajouter facilement tout ce dont vous avez besoin pour utiliser Azure Key Vault en vue de gérer les secrets de vos projets web dans Visual Studio, que vous utilisiez ASP.NET Core ou tout type de projet ASP.NET. À l’aide de la fonctionnalité Services connectés dans Visual Studio, vous pouvez avoir Visual Studio ajoute automatiquement tous les packages NuGet et les paramètres de configuration que vous devez vous connecter à Key Vault dans Azure. 

Pour plus d’informations sur les changements qu’apportent les Services connectés à votre projet pour l’activation de Key Vault, consultez [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](#how-your-aspnet-framework-project-is-modified) ou [Key Vault Connected Service - What happened to my ASP.NET Core project](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Conditions préalables

- **Un abonnement Azure**. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019** ou **Visual Studio 2017 version 15.7** avec la **développement Web** charge de travail installée. [Téléchargez-le maintenant](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Pour ASP.NET (pas cœur) avec Visual Studio 2017, vous devez le .NET Framework 4.7.1 ou version ultérieure à des outils de développement, qui ne sont pas installés par défaut. Pour les installer, lancez Visual Studio Installer, choisissez **Modifier**, puis choisissez des **Composants individuels**. Ensuite, sur la droite, développez **Développement web ASP.NET**, puis choisissez **Outils de développement .NET Framework 4.7.1**.
- ASP.NET 4.7.1 ou version ultérieure, ou ouvrez un projet web ASP.NET Core 2.0.

## <a name="add-key-vault-support-to-your-project"></a>Ajouter la prise en charge Key Vault à votre projet

1. Dans **l’Explorateur de solutions**, choisissez **Ajouter** > **Service connecté**.
   La page Service connecté s’affiche et montre des services que vous pouvez ajouter à votre projet.
1. Dans le menu des services disponibles, choisissez **Sécuriser les secrets avec Azure Key Vault**.

   ![Sélection de l’option Sécuriser les secrets avec Azure Key Vault](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Si vous êtes connecté à Visual Studio et si un abonnement Azure est associé à votre compte, une page s’affiche et montre la liste déroulante de vos abonnements. Vérifiez que vous êtes connecté à Visual Studio et que le compte auquel vous êtes connecté est identique à celui que vous utilisez pour votre abonnement Azure.

1. Sélectionnez l’abonnement que vous souhaitez utiliser. Ensuite, choisissez un coffre de clés nouveau ou existant, ou cliquez sur le lien Modifier pour modifier le nom généré automatiquement.

   ![Sélectionnez votre abonnement](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Tapez le nom que vous souhaitez utiliser pour le coffre de clés.

   ![Renommer le coffre de clés et choisir un groupe de ressources](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Sélectionnez un groupe de ressources existant, ou choisissez de créer un nouveau avec un nom unique généré automatiquement.  Si vous souhaitez créer un groupe et lui attribuer un nom différent, vous pouvez utiliser le [portail Azure](https://portal.azure.com), puis fermer la page et redémarrer pour recharger la liste des groupes de ressources.
1. Choisissez la région dans laquelle créer le coffre de clés. Si votre application web est hébergée dans Azure, choisissez la région qui héberge l’application web pour des performances optimales.
1. Sélectionnez un modèle de tarification. Pour plus d’informations, consultez [Tarification Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Choisissez OK pour accepter les options de configuration.
1. Choisissez **Ajouter** pour créer le coffre de clés. Le processus de création peut échouer si vous choisissez un nom qui a déjà été utilisé.  Si cela se produit, utilisez le lien **Modifier** pour renommer le coffre de clés, puis réessayez.

   ![Ajout d’un service connecté au projet](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Maintenant, ajoutez un secret à votre coffre de clés dans Azure. Pour accéder au bon endroit du portail, cliquez sur le lien permettant de gérer les secrets stockés dans le coffre de clés. Si vous avez fermé la page ou le projet, vous pouvez y accéder dans le [portail Azure](https://portal.azure.com). Pour cela, choisissez **Tous les services**, puis, sous **Sécurité**, choisissez **Coffre de clés** et sélectionnez le coffre de clés que vous avez créé.

   ![Accès au portail](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Dans la section Coffre de clés du coffre de clés que vous avez créé, choisissez **Secrets**, puis **Générer/Importer**.

   ![Générer/Importer un secret](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Entrez un secret, tel que « MonSecret », et attribuez-lui une valeur de chaîne telle que « test », puis choisissez le bouton **Créer**.

   ![Créer un secret](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (facultatif) Entrez un autre secret, mais cette fois-ci, placez-le dans une catégorie en le nommant « Secrets--MonSecret ». Cette syntaxe spécifie la catégorie « Secrets » qui contient le secret « MonSecret ».
 
Maintenant, vous pouvez accéder à vos secrets dans le code. Les étapes suivantes sont différentes selon que vous utilisez ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Accéder à vos secrets dans le code

1. Installez ces deux packages NuGet : [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) et [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Ouvrez le fichier Program.cs et mettez à jour le code avec le code suivant : 
   ```
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
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. Ouvrez ensuite le fichier de About.cshtml.cs et écrire le code suivant :
   1. Inclure une référence à Microsoft.Extensions.Configuration par cette instruction using :

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Ajoutez ce constructeur :

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Mettez à jour la méthode OnGet. Mettre à jour la valeur d’espace réservé indiquée ici avec le nom de secret que vous avez créé dans les commandes ci-dessus.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Exécutez l’application localement en accédant à la page About. Vous devez voir votre valeur secrète récupérée.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le coffre de clés et les ressources qui lui sont associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce Démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)**, tapez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Comment votre projet ASP.NET Core est modifié

Cette section identifie les modifications exactes apportées à un projet ASP.NET lors de l’ajout du coffre de clés de service à l’aide de Visual Studio connecté.

### <a name="added-references"></a>Références ajoutées

Affecte les références .NET du fichier projet et les références de package NuGet.

| Type | Référence |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>Fichiers ajoutés

- Ajout de ConnectedService.json, qui enregistre des informations sur le fournisseur et la version du service connecté, ainsi qu’un lien vers la documentation.

### <a name="project-file-changes"></a>Modifications apportées au fichier projet

- Ajout de l’ItemGroup des services connectés et du fichier ConnectedServices.json.

### <a name="launchsettingsjson-changes"></a>Modifications de launchsettings.json

- Ajout des entrées de variables d’environnement suivantes au profil IIS Express et au profil qui correspond au nom de votre projet web :

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Modifications apportées à Azure

- Création d’un groupe de ressources (ou utilisation d’un groupe existant).
- Création d’un coffre de clés dans le groupe de ressources spécifié.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Comment votre projet de l’infrastructure ASP.NET est modifié

Cette section identifie les modifications exactes apportées à un projet ASP.NET lors de l’ajout du coffre de clés de service à l’aide de Visual Studio connecté.

### <a name="added-references"></a>Références ajoutées

Affecte les références .NET du fichier projet et `packages.config` (références NuGet).

| Type | Référence |
| --- | --- |
| .NET ; NuGet | Microsoft.Azure.KeyVault |
| .NET ; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET ; NuGet | Microsoft.Rest.ClientRuntime |
| .NET ; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>Fichiers ajoutés

- Ajout de ConnectedService.json, qui enregistre des informations sur le fournisseur et la version du service connecté, ainsi qu’un lien vers la documentation.

### <a name="project-file-changes"></a>Modifications apportées au fichier projet

- Ajout de l’ItemGroup des services connectés et du fichier ConnectedServices.json.
- Références aux assemblys .NET décrits dans la section [Références ajoutées](#added-references).

### <a name="webconfig-or-appconfig-changes"></a>Modifications apportées à web.config ou à app.config

- Ajout des entrées de configuration suivantes :

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure"></a>Modifications apportées à Azure

- Création d’un groupe de ressources (ou utilisation d’un groupe existant).
- Création d’un coffre de clés dans le groupe de ressources spécifié.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le développement Key Vault en lisant le [Guide du développeur Key Vault](key-vault-developers-guide.md)