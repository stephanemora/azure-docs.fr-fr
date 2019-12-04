---
title: Ajouter la prise en charge de Key Vault à votre projet ASP.NET à l’aide de Visual Studio - Azure Key Vault | Microsoft Docs
description: Ce tutoriel vous montre comment ajouter la prise en charge de Key Vault à une application web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1df0ff3b6fea335dde5a3200f824adf14f924d9c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452369"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Ajouter Key Vault à votre application web à l’aide des Services connectés Visual Studio

Dans ce tutoriel, vous allez voir comment ajouter facilement tout ce dont vous avez besoin pour utiliser Azure Key Vault en vue de gérer les secrets de vos projets web dans Visual Studio, que vous utilisiez ASP.NET Core ou tout type de projet ASP.NET. Quand vous utilisez la fonctionnalité Services connectés de Visual Studio, Visual Studio peut ajouter automatiquement tous les packages NuGet et les paramètres de configuration dont vous avez besoin pour vous connecter à Key Vault dans Azure.

Pour plus d’informations sur les changements qu’apportent les Services connectés à votre projet pour l’activation de Key Vault, consultez [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](#how-your-aspnet-framework-project-is-modified) ou [Key Vault Connected Service - What happened to my ASP.NET Core project](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Prérequis

- **Un abonnement Azure**. Si vous n’avez pas d’abonnement, inscrivez-vous pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 version 16.3** ou ultérieure, ou **Visual Studio 2017 version 15.7** avec installation de la charge de travail **Développement web**. [Téléchargez-le maintenant](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Pour ASP.NET (et non pour ASP.NET Core) avec Visual Studio 2017, vous avez besoin des outils de développement .NET Framework 4.7.1 ou version ultérieure, qui ne sont pas installés par défaut. Pour les installer, lancez Visual Studio Installer, choisissez **Modifier**, puis choisissez des **Composants individuels**. Ensuite, sur la droite, développez **Développement web ASP.NET**, puis choisissez **Outils de développement .NET Framework 4.7.1**.
- Un projet web ASP.NET 4.7.1 ou version ultérieure ou ASP.NET Core 2.0 ou version ultérieure s’ouvre.

## <a name="add-key-vault-support-to-your-project"></a>Ajouter la prise en charge Key Vault à votre projet

Avant de commencer, vérifiez que vous êtes connecté à Visual Studio. Connectez-vous avec le même compte que celui que vous utilisez pour votre abonnement Azure. Ouvrez ensuite un projet web ASP.NET 4.7.1 ou version ultérieure, ou ASP.NET Core 2.0, puis procédez comme suit :

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet auquel vous souhaitez ajouter la prise en charge de Key Vault, puis choisissez **Ajouter** > **Service connecté**.
   La page Service connecté s’affiche et montre des services que vous pouvez ajouter à votre projet.
1. Dans le menu des services disponibles, choisissez **Sécuriser les secrets avec Azure Key Vault**.

   ![Sélection de l’option Sécuriser les secrets avec Azure Key Vault](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Sélectionnez l’abonnement que vous souhaitez utiliser, puis choisissez un coffre de clés nouveau ou existant. Si vous choisissez le nouveau coffre de clés, un lien **Modifier** s’affiche. Sélectionnez-le pour configurer votre nouveau coffre de clés.

   ![Sélectionnez votre abonnement](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. Dans **Edit Azure Key Vault** (Modifier le coffre de clés Azure), entrez le nom que vous souhaitez utiliser pour le coffre de clés.

1. Sélectionnez un **groupe de ressources** existant, ou créez-en un avec un nom unique généré automatiquement.  Si vous souhaitez créer un groupe et lui attribuer un nom différent, vous pouvez utiliser le [portail Azure](https://portal.azure.com), puis fermer la page et redémarrer pour recharger la liste des groupes de ressources.
1. Choisissez l’**emplacement** dans lequel créer le coffre de clés. Si votre application web est hébergée dans Azure, choisissez la région qui héberge l’application web pour des performances optimales.
1. Choisissez un **niveau tarifaire**. Pour plus d’informations, consultez [Tarification Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Choisissez **OK** pour accepter les options de configuration.
1. Après avoir sélectionné un coffre de clés existant ou configuré un nouveau coffre de clés, sous l’onglet **Azure Key Vault** de Visual Studio, sélectionnez **Ajouter** pour ajouter le service connecté.
1. Sélectionnez le lien **Gérer les secrets stockés dans ce Key Vault** pour ouvrir la page **Secrets** de votre coffre de clés. Si vous avez fermé la page ou le projet, vous pouvez y accéder dans le [portail Azure](https://portal.azure.com). Pour cela, choisissez **Tous les services** puis, sous **Sécurité**, choisissez **Coffre de clés** et sélectionnez votre coffre de clés.
1. Dans la section Key Vault du Key Vault que vous avez créé, choisissez **Secrets**, puis **Générer/Importer**.

   ![Générer/Importer un secret](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Entrez un secret, tel que *MonSecret*, et attribuez-lui une valeur de chaîne comme test, puis sélectionnez le bouton **Créer**.

   ![Créer un secret](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (facultatif) Entrez un autre secret, mais cette fois-ci, placez-le dans une catégorie en le nommant *Secrets--MonSecret*. Cette syntaxe spécifie la catégorie « Secrets » qui contient le secret « MonSecret ».

Maintenant, vous pouvez accéder à vos secrets dans le code. Les étapes suivantes sont différentes selon que vous utilisez ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Accéder à vos secrets dans le code (ASP.NET Core)

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**. Sous l’onglet **Parcourir**, localisez et installez les deux packages NuGet suivants : [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) et ajoutez [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) pour .NET Core 2 ou [Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) pour .NET Core 3.

1. Pour .NET Core 2, sélectionnez l’onglet `Program.cs` et remplacez la définition `BuildWebHost` de la classe Programme par ce qui suit :

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
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
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Pour .NET Core 3, utilisez le code suivant.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://WebApplication4-3-kv.vault.azure.net";
    ```

1. Ensuite, ouvrez l’un des fichiers de page, par exemple *Index.cshtml.cs* et écrivez le code suivant :
   1. Incluez une référence à `Microsoft.Extensions.Configuration` à l’aide de cette directive :

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Ajoutez la variable de configuration.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Ajoutez ce constructeur ou remplacez le constructeur existant par ce qui suit :

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Mettez à jour la méthode `OnGet`. Mettez à jour la valeur d’espace réservé indiquée ici avec le nom de secret que vous avez créé dans les commandes ci-dessus.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Pour confirmer la valeur au moment de l’exécution, ajoutez du code pour afficher `ViewData["Message"]` sur le fichier *.cshtml* pour afficher le secret dans un message.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Vous pouvez exécuter l’application localement pour vérifier que le secret est correctement obtenu à partir de Key Vault.

## <a name="access-your-secrets-aspnet"></a>Accéder à vos secrets (ASP.NET)

Vous pouvez paramétrer la configuration de sorte que le fichier web.config ait une valeur factice dans l’élément `appSettings` qui est remplacé par la valeur true au moment de l’exécution. Vous pouvez ensuite y accéder via la structure de données `ConfigurationManager.AppSettings`.

1. Modifiez votre fichier web.config.  Recherchez la balise appSettings, ajoutez un attribut `configBuilders="AzureKeyVault"`et ajoutez une ligne :

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Modifiez la méthode `About` dans *HomeController.cs* pour afficher la valeur de confirmation.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Exécutez l’application localement sous le débogueur, basculez dans l’onglet **À propos** et vérifiez que la valeur provenant de Key Vault est affichée.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le coffre de clés et les ressources qui lui sont associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans la zone **TAPEZ LE NOM DU GROUPE DE RESSOURCES :** , entrez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="troubleshooting"></a>Résolution de problèmes

Si votre Key Vault s’exécute sur un autre compte Microsoft que celui auquel vous êtes connecté à Visual Studio (par exemple, Key Vault s’exécute sur votre compte professionnel, alors que Visual Studio utilise votre compte privé), un message d’erreur indiquant que Visual Studio ne peut pas avoir accès à Key Vault est ajouté à votre fichier Program.cs. Pour résoudre ce problème :

1. Accédez au [Portail Azure](https://portal.azure.com) et accédez à votre coffre de clés.

1. Choisissez **Stratégies d’accès**, puis **Ajouter une stratégie d’accès**et choisissez le compte avec lequel vous êtes connecté en tant que principal.

1. Dans Visual Studio, choisissez **Fichier** > **Paramètres du compte**.
Sélectionnez **Ajouter un compte** dans la section **Tous les comptes**. Connectez-vous avec le compte que vous avez choisi comme principal de votre stratégie d’accès.

1. Choisissez **Outils** > **Options** et recherchez **Authentification du service Azure**. Sélectionnez ensuite le compte que vous venez d’ajouter à Visual Studio.

Désormais, quand vous déboguez votre application, Visual Studio se connecte au compte sur lequel se trouve votre Key Vault.

## <a name="how-your-aspnet-core-project-is-modified"></a>Modifications apportées à votre projet ASP.NET Core

Cette section identifie les changements exacts apportés à un projet ASP.NET lors de l’ajout du Service connecté Key Vault avec Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Références ajoutées pour ASP.NET Core

Affecte les références .NET du fichier projet et les références de package NuGet.

| Type | Informations de référence |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Fichiers ajoutés pour ASP.NET Core

- Ajout de `ConnectedService.json`, qui enregistre des informations sur le fournisseur et la version du service connecté, ainsi qu’un lien vers la documentation.

### <a name="project-file-changes-for-aspnet-core"></a>Modifications apportées au fichier projet pour ASP.NET Core

- Ajout de l’ItemGroup des services connectés et du fichier `ConnectedServices.json`.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>Modifications de launchsettings.json pour ASP.NET Core

- Ajout des entrées de variables d’environnement suivantes au profil IIS Express et au profil qui correspond au nom de votre projet web :

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Modifications apportées à Azure pour ASP.NET Core

- Création d’un groupe de ressources (ou utilisation d’un groupe existant).
- Création d’un coffre de clés dans le groupe de ressources spécifié.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Modifications apportées à votre projet ASP.NET Framework

Cette section identifie les changements exacts apportés à un projet ASP.NET lors de l’ajout du Service connecté Key Vault avec Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Références ajoutées pour ASP.NET Framework

Affecte les références .NET du fichier projet et `packages.config` (références NuGet).

| Type | Informations de référence |
| --- | --- |
| .NET ; NuGet | Microsoft.Azure.KeyVault |
| .NET ; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET ; NuGet | Microsoft.Rest.ClientRuntime |
| .NET ; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Fichiers ajoutés pour ASP.NET Framework

- Ajout de `ConnectedService.json`, qui enregistre des informations sur le fournisseur et la version du service connecté, ainsi qu’un lien vers la documentation.

### <a name="project-file-changes-for-aspnet-framework"></a>Modifications apportées au fichier projet pour ASP.NET Framework

- Ajout de l’ItemGroup des services connectés et du fichier ConnectedServices.json.
- Références aux assemblys .NET décrits dans la section [Références ajoutées](#added-references-for-aspnet-framework).

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Modifications apportées à Azure pour ASP.NET Framework

- Création d’un groupe de ressources (ou utilisation d’un groupe existant).
- Création d’un coffre de clés dans le groupe de ressources spécifié.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez suivi ce tutoriel, vos autorisations Key Vault sont configurées pour s’exécuter avec votre propre abonnement Azure, mais cela peut ne pas être souhaitable pour un scénario de production. Vous pouvez créer une identité managée afin de gérer l’accès à Key Vault pour votre application. Consultez [Fournir une authentification Key Vault avec une identité managée](/azure/key-vault/managed-identity).

Découvrez plus en détail le développement de coffres de clés en lisant le [Guide du développeur de coffre de clés](key-vault-developers-guide.md).
