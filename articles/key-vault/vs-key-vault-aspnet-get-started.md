---
title: Bien démarrer avec le service connecté Key Vault dans Visual Studio (projets ASP.NET) | Microsoft Docs
description: Ce tutoriel vous montre comment ajouter la prise en charge de Key Vault à une application web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: cd305801f10c899682aa6d751e48f30b6e8303fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781618"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Bien démarrer avec le service connecté Key Vault dans Visual Studio (projets ASP.NET)

> [!div class="op_single_selector"]
> - [Prise en main](vs-key-vault-aspnet-get-started.md)
> - [Que s'est-il passé ?](vs-key-vault-aspnet-what-happened.md)

Cet article offre une aide complémentaire une fois que vous avez ajouté Key Vault à un projet ASP.NET à l’aide de la commande **Ajouter des services connectés** de Visual Studio. Si vous n’avez pas déjà ajouté le service à votre projet, vous pouvez le faire à tout moment en suivant les instructions contenues dans [Ajouter Key Vault à votre application web à l’aide des Services connectés Visual Studio](vs-key-vault-add-connected-service.md).

Consultez [Qu’est-il arrivé à mon projet ASP.NET ?](vs-key-vault-aspnet-core-what-happened.md) pour connaître les modifications qui ont été apportées à votre projet après l’ajout du service connecté.

## <a name="after-you-connect"></a>Après la connexion

1. Ajoutez un secret à votre coffre de clés dans Azure. Pour accéder au bon endroit du portail, cliquez sur le lien permettant de **gérer les secrets stockés dans le coffre de clés**. Si vous avez fermé la page ou le projet, vous pouvez y accéder dans le [portail Azure](https://portal.azure.com). Pour cela, choisissez **Tous les services**, puis, sous **Sécurité**, choisissez **Coffre de clés** et sélectionnez le coffre de clés que vous venez de créer.

   ![Accès au portail](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Dans la section Coffre de clés du coffre de clés que vous avez créé, choisissez **Secrets**, puis **Générer/Importer**.

   ![Générer/Importer un secret](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Entrez un secret, tel que **MonSecret**, et attribuez-lui une valeur de chaîne telle que « test », puis choisissez le bouton **Créer**.

   ![Créer un secret](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (facultatif) Entrez un autre secret, mais cette fois-ci, placez-le dans une catégorie en le nommant **Secrets--MonSecret**. Cette syntaxe spécifie la catégorie **Secrets** qui contient le secret **MonSecret**.

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

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le coffre de clés et les ressources qui lui sont associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce Démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)**, tapez le nom du groupe de ressources et sélectionnez **Supprimer**.

# <a name="next-steps"></a>Étapes suivantes

Découvrez le développement avec Key Vault en lisant le [Guide du développeur Key Vault](key-vault-developers-guide.md)