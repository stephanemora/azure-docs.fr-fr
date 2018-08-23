---
title: Bien démarrer avec le service connecté Key Vault dans Visual Studio (projets ASP.NET Core) | Microsoft Docs
description: Ce tutoriel vous montre comment ajouter la prise en charge Key Vault à une application web ASP.NET ou ASP.NET Core.
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
ms.openlocfilehash: e591771ee9c9cb12d9ec2ff61ec7f5a76691c8c7
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146495"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Bien démarrer avec le service connecté Key Vault dans Visual Studio (projets ASP.NET Core)

> [!div class="op_single_selector"]
> - [Prise en main](vs-key-vault-aspnet-core-get-started.md)
> - [Que s'est-il passé ?](vs-key-vault-aspnet-core-what-happened.md)

Cet article offre une aide complémentaire une fois que vous avez ajouté Key Vault à un projet ASP.NET Core à l’aide de la commande **Ajouter des services connectés** de Visual Studio. Si vous n’avez pas déjà ajouté le service à votre projet, vous pouvez le faire à tout moment en suivant les instructions contenues dans [Ajouter Key Vault à votre application web à l’aide des Services connectés Visual Studio](vs-key-vault-add-connected-service.md).

Consultez [Qu’est-il arrivé à mon projet ASP.NET Core ?](vs-key-vault-aspnet-core-what-happened.md) pour connaître les modifications qui ont été apportées à votre projet après l’ajout du service connecté.

## <a name="after-you-connect"></a>Après la connexion

1. Ajoutez un secret à votre coffre de clés dans Azure. Pour accéder au bon endroit du portail, cliquez sur le lien permettant de gérer les secrets stockés dans le coffre de clés. Si vous avez fermé la page ou le projet, vous pouvez y accéder dans le [portail Azure](https://portal.azure.com). Pour cela, choisissez **Tous les services**, puis, sous **Sécurité**, choisissez **Coffre de clés** et sélectionnez le coffre de clés que vous venez de créer.

   ![Accès au portail](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Dans la section Coffre de clés du coffre de clés que vous avez créé, choisissez **Secrets**, puis **Générer/Importer**.

   ![Générer/Importer un secret](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Entrez un secret, tel que « MonSecret », et attribuez-lui une valeur de chaîne telle que « test », puis choisissez le bouton **Créer**.

   ![Créer un secret](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (facultatif) Entrez un autre secret, mais cette fois-ci, placez-le dans une catégorie en le nommant **Secrets--MonSecret**. Cette syntaxe spécifie la catégorie **Secrets** qui contient le secret **MonSecret**.
1. Dans votre projet Visual Studio, vous pouvez maintenant référencer ces secrets dans le code à l’aide des expressions suivantes :
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Vous savez désormais que votre application web est capable d’utiliser le coffre de clés pour accéder aux secrets qui sont stockés de manière sécurisée.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le coffre de clés et les ressources qui lui sont associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce Démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)**, tapez le nom du groupe de ressources et sélectionnez **Supprimer**.

# <a name="next-steps"></a>Étapes suivantes

Découvrez le développement avec Key Vault en lisant le [Guide du développeur Key Vault](key-vault-developers-guide.md)