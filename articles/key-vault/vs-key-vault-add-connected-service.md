---
title: Ajouter la prise en charge Key Vault à votre projet ASP.NET à l’aide de Visual Studio | Microsoft Docs
description: Ce tutoriel vous montre comment ajouter la prise en charge de Key Vault à une application web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: d2ab34b3737ec00e4adc464f6d2255203fb6ae08
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2018
ms.locfileid: "43840617"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Ajouter Key Vault à votre application web à l’aide des Services connectés Visual Studio

Dans ce tutoriel, vous allez voir comment ajouter facilement tout ce dont vous avez besoin pour utiliser Azure Key Vault en vue de gérer les secrets de vos projets web dans Visual Studio, que vous utilisiez ASP.NET Core ou tout type de projet ASP.NET. Quand vous utilisez la fonctionnalité Services connectés de Visual Studio 2017, Visual Studio ajoute automatiquement tous les packages NuGet et les paramètres de configuration dont vous avez besoin pour vous connecter à Key Vault dans Azure. 

Pour plus d’informations sur les changements qu’apportent les Services connectés à votre projet pour l’activation de Key Vault, consultez [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](vs-key-vault-aspnet-what-happened.md) ou [Key Vault Connected Service - What happened to my ASP.NET Core project](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Prérequis

- **Un abonnement Azure**. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 version 15.7** avec installation de la charge de travail **Développement web**. [Téléchargez-le maintenant](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Pour ASP.NET (et non pour ASP.NET Core), vous avez besoin des outils de développement .NET Framework 4.7.1, qui ne sont pas installés par défaut. Pour les installer, lancez Visual Studio Installer, choisissez **Modifier**, puis choisissez des **Composants individuels**. Ensuite, sur la droite, développez **Développement web ASP.NET**, puis choisissez **Outils de développement .NET Framework 4.7.1**.
- Un projet web ASP.NET 4.7.1 ou ASP.NET Core 2.0 s’ouvre.

## <a name="add-key-vault-support-to-your-project"></a>Ajouter la prise en charge Key Vault à votre projet

1. Dans **l’Explorateur de solutions**, choisissez **Ajouter** > **Service connecté**.
   La page Service connecté s’affiche et montre des services que vous pouvez ajouter à votre projet.
1. Dans le menu des services disponibles, choisissez **Sécuriser les secrets avec Azure Key Vault**.

   ![Sélection de l’option Sécuriser les secrets avec Azure Key Vault](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Si vous êtes connecté à Visual Studio et si un abonnement Azure est associé à votre compte, une page s’affiche et montre la liste déroulante de vos abonnements.
1. Sélectionnez l’abonnement que vous souhaitez utiliser. Ensuite, choisissez un coffre de clés nouveau ou existant, ou cliquez sur le lien Modifier pour modifier le nom généré automatiquement.

   ![Sélectionnez votre abonnement](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Tapez le nom que vous souhaitez utiliser pour le coffre de clés.

   ![Renommer le coffre de clés et choisir un groupe de ressources](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Sélectionnez un groupe de ressources existant, ou créez-en un avec un nom unique généré automatiquement.  Si vous souhaitez créer un groupe et lui attribuer un nom différent, vous pouvez utiliser le [portail Azure](https://portal.azure.com), puis fermer la page et redémarrer pour recharger la liste des groupes de ressources.
1. Choisissez la région dans laquelle créer le coffre de clés. Si votre application web est hébergée dans Azure, choisissez la région qui héberge l’application web pour des performances optimales.
1. Sélectionnez un modèle de tarification. Pour plus d’informations, consultez [Tarification Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Choisissez OK pour accepter les options de configuration.
1. Choisissez **Ajouter** pour créer le coffre de clés. Le processus de création peut échouer si vous choisissez un nom qui a déjà été utilisé.  Si cela se produit, utilisez le lien **Modifier** pour renommer le coffre de clés, puis réessayez.

   ![Ajout d’un service connecté au projet](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Maintenant, ajoutez un secret à votre coffre de clés dans Azure. Pour accéder au bon endroit du portail, cliquez sur le lien permettant de gérer les secrets stockés dans le coffre de clés. Si vous avez fermé la page ou le projet, vous pouvez y accéder dans le [portail Azure](https://portal.azure.com). Pour cela, choisissez **Tous les services**, puis, sous **Sécurité**, choisissez **Coffre de clés** et sélectionnez le coffre de clés que vous venez de créer.

   ![Accès au portail](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Dans la section Coffre de clés du coffre de clés que vous avez créé, choisissez **Secrets**, puis **Générer/Importer**.

   ![Générer/Importer un secret](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Entrez un secret, tel que « MonSecret », et attribuez-lui une valeur de chaîne telle que « test », puis choisissez le bouton **Créer**.

   ![Créer un secret](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (facultatif) Entrez un autre secret, mais cette fois-ci, placez-le dans une catégorie en le nommant « Secrets--MonSecret ». Cette syntaxe spécifie la catégorie « Secrets » qui contient le secret « MonSecret ».
 
Maintenant, vous pouvez accéder à vos secrets dans le code. Les étapes suivantes sont différentes selon que vous utilisez ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Accéder à vos secrets dans le code (projets ASP.NET Core)

La connexion à Key Vault est configurée au démarrage par une classe qui implémente [Microsoft.AspNetCore.Hosting.IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) en étendant le comportement de démarrage décrit dans [Améliorer une application à partir d’un assembly externe dans ASP.NET Core avec IHostingStartup](/aspnet/core/fundamentals/host/platform-specific-configuration). La classe de démarrage utilise deux variables d’environnement qui contiennent les informations de connexion du coffre de clés : ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED, défini sur true, et ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT, défini sur l’URL de votre coffre de clés. Ces informations sont ajoutées au fichier launchsettings.json quand vous exécutez le processus **Ajouter un service connecté**.

Pour accéder à vos secrets :

1. Dans Visual Studio, dans votre projet ASP.NET Core, vous pouvez maintenant référencer ces secrets dans le code à l’aide des expressions suivantes :
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. Dans une page .cshtml, par exemple About.cshtml, ajoutez la directive @inject au début du fichier pour définir une variable que vous pouvez utiliser pour accéder à la configuration du coffre de clés.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. À des fins de test, vous pouvez vérifier que la valeur du secret est disponible en l’affichant dans l’une des pages. Utilisez @config pour référencer la variable de configuration.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Générez, puis exécutez l’application web, puis accédez à la page À propos pour voir la valeur du secret.

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Accéder à vos secrets dans le code (projets ASP.NET 4.7.1)

La connexion à votre coffre de clés est configurée par la classe ConfigurationBuilder à l’aide des informations qui ont été ajoutées à votre fichier web.config quand vous avez exécuté le processus **Ajouter un service connecté**.

Pour accéder à vos secrets :

1. Modifiez le fichier web.config de la façon suivante. Les clés sont des espaces réservés que le ConfigurationBuilder AzureKeyVault doit remplacer par les valeurs des secrets du coffre de clés.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. Dans le fichier HomeController, dans la méthode de contrôleur About, ajoutez les lignes suivantes pour récupérer le secret et le stocker dans ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. Dans la vue About.cshtml, ajoutez ce qui suit pour afficher la valeur du secret (à des fins de test uniquement).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Vous savez désormais que votre application web est capable d’utiliser le coffre de clés pour accéder aux secrets qui sont stockés de manière sécurisée.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le coffre de clés et les ressources qui lui sont associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce Démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)**, tapez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le développement Key Vault en lisant le [Guide du développeur Key Vault](key-vault-developers-guide.md)