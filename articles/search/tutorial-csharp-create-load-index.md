---
title: 'Tutoriel .NET : Ajouter une recherche à des applications web'
titleSuffix: Azure Cognitive Search
description: Créez un index et importez des données CSV dans l’index de recherche avec .NET.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: b7bbf9f1ec572680f02e4b6d3adca6d807b45f83
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107981954"
---
# <a name="2---create-and-load-search-index-with-net"></a>2 - Créer et charger un index de recherche avec .NET

Continuez à créer votre site web de recherche en procédant comme suit :
* Création d’une ressource de recherche avec l’extension VS Code
* Création d’un index et importation de données avec .NET à l’aide de l’exemple de script et du SDK Azure [Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/).

## <a name="create-an-azure-search-resource"></a>Créer une ressource Recherche Azure 

Créez une nouvelle ressource de recherche avec l’extension [Recherche cognitive Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) pour Visual Studio Code.

1. Dans Visual Studio Code, ouvrez la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface) et sélectionnez l’icône Azure. 

1. Dans la barre latérale, **cliquez avec le bouton droit sur votre abonnement Azure** sous la zone `Azure: Cognitive Search`, puis sélectionnez **Créer un service de recherche**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Dans la barre latérale, cliquez avec le bouton droit sur votre abonnement Azure sous la zone **Recherche cognitive Azure**, puis sélectionnez **Créer un service de recherche**.":::

1. Suivez les invites pour fournir les informations suivantes :

    |Prompt|Entrez|
    |--|--|
    |Entrez un nom global unique pour le nouveau service de recherche.|**Mémorisez ce nom**. Ce nom de ressource devient partie intégrante de votre point de terminaison de ressource.|
    |Sélectionner un groupe de ressources pour les nouvelles ressources|Utilisez le groupe de ressources que vous avez créé pour ce tutoriel.|
    |Sélectionnez la référence SKU de votre service de recherche.|Pour les besoins de ce tutoriel, sélectionnez **Free (Gratuit)** . Vous ne pouvez pas modifier un niveau tarifaire SKU une fois le service créé.|
    |Sélectionnez un emplacement pour les nouvelles ressources.|Sélectionnez une région proche de chez vous.|

1. Une fois que vous avez terminé les invites, votre nouvelle ressource de recherche est créée. 

## <a name="get-your-search-resource-admin-key"></a>Récupérer la clé d’administration de la ressource de recherche

Récupérez la clé d’administration de votre ressource de recherche avec l’extension Visual Studio Code. 

1. Dans Visual Studio Code, dans la barre latérale, cliquez avec le bouton droit sur votre ressource de recherche, puis sélectionnez **Copier la clé d’administration**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Dans la barre latérale, cliquez avec le bouton droit sur votre ressource de recherche, puis sélectionnez **Copier la clé d’administration**":::.

1. Conservez cette clé d’administration, vous devrez l’utiliser dans [une section ultérieure](#prepare-the-bulk-import-script-for-search). 

## <a name="prepare-the-bulk-import-script-for-search"></a>Préparer le script d’importation en bloc pour la recherche

Le script utilise le Kit de développement logiciel (SDK) Azure pour Recherche cognitive :

* [Package NuGet Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
* [Documentation de référence](/dotnet/api/overview/azure/search)

1. Dans Visual Studio Code, ouvrez le fichier `Program.cs` dans le sous-répertoire `search-website/bulk-insert` et remplacez les variables suivantes par vos propres valeurs pour vous authentifier auprès du Kit de développement logiciel (SDK) Azure Search :

    * VOTRE-NOM-DE-RESSOURCE-DE-RECHERCHE
    * VOTRE CLÉ-ADMINISTRATION-DE-RECHERCHE

    :::code language="csharp" source="~/azure-search-dotnet-samples/search-website/bulk-insert/Program.cs" highlight="16-19" :::

1. Ouvrez un terminal intégré dans Visual Studio Code pour le sous-répertoire du répertoire du projet, `search-website/bulk-insert`, puis exécutez la commande suivante pour installer les dépendances. 

    ```bash
    dotnet restore
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Exécuter le script d’importation en bloc pour la recherche

1. Continuez à utiliser le terminal intégré dans Visual Studio pour le sous-répertoire du répertoire du projet, `search-website/bulk-insert`, pour exécuter la commande bash suivante afin d’exécuter le script `Program.cs` :

    ```bash
    dotnet run
    ```

1. À mesure que le code s’exécute, la console affiche la progression. 
1. Une fois le chargement terminé, la dernière instruction imprimée dans la console stipule la « fin de l’insertion en bloc des données des livres ».

## <a name="review-the-new-search-index"></a>Examiner le nouvel index de recherche

Une fois le téléchargement terminé, l’index de recherche est prêt à être utilisé. Examinez votre nouvel index.

1. Dans Visual Studio Code, ouvrez l’extension Recherche cognitive Azure et sélectionnez votre ressource de recherche.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="Dans Visual Studio Code, ouvrez l’extension Recherche cognitive Azure et ouvrez votre ressource de recherche.":::

1. Développez Index, puis Documents, puis `good-books`, puis sélectionnez un document pour en afficher toutes les données spécifiques.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Développez Index, puis « good-books », puis sélectionnez un document.":::

## <a name="copy-your-search-resource-name"></a>Copiez le nom de votre ressource de recherche.

Notez le **nom de votre ressource de recherche**. Vous en aurez besoin pour connecter l’application Azure Function à votre ressource de recherche. 

> [!CAUTION]
> Vous pouvez être tenté d’utiliser votre clé d’administration de recherche dans Azure Function, qui ne suit pas le principe du privilège minimum. Azure Function utilise la clé de requête pour se conformer au privilège minimum. 

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre application web statique](tutorial-csharp-deploy-static-web-app.md)