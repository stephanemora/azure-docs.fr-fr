---
title: 'Tutoriel .NET : Vue d’ensemble de l’intégration de la recherche'
titleSuffix: Azure Cognitive Search
description: Présentation technique et configuration pour l’ajout de la recherche à un site web et le déploiement sur Azure Static Web App avec .NET.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: 49919a5a0efbdae46f2ef82a7129fee8aa686e8e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111964033"
---
# <a name="1---overview-of-adding-search-to-a-website-with-net"></a>1 - Vue d’ensemble de l’ajout de la recherche à un site web avec .NET

Ce tutoriel crée un site web pour effectuer une recherche dans un catalogue de livres, puis déploie le site web vers une application web statique Azure. 

L’application est disponible : 
* [Exemple](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website)
* [Site web de démonstration - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Que fait l’exemple ? 

Cet exemple de site web permet d’accéder à un catalogue de 10 000 livres. Un utilisateur peut effectuer une recherche dans le catalogue en entrant du texte dans la barre de recherche. Lorsque l’utilisateur entre du texte, le site web utilise la fonctionnalité de suggestion de l’index de recherche pour compléter le texte. Une fois la requête terminée, la liste des livres s’affiche avec une partie des détails. Un utilisateur peut sélectionner un livre pour afficher tous les détails du livre, stockés dans l’index de recherche. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Cet exemple de site web permet d’accéder à un catalogue de 10 000 livres. Un utilisateur peut effectuer une recherche dans le catalogue en entrant du texte dans la barre de recherche. Lorsque l’utilisateur entre du texte, le site web utilise la fonctionnalité de suggestion de l’index de recherche pour compléter le texte. Une fois la recherche terminée, la liste des livres s’affiche avec une partie des détails. Un utilisateur peut sélectionner un livre pour afficher tous les détails du livre, stockés dans l’index de recherche.":::

L’expérience de recherche comprend les éléments suivants : 

* Recherche : fournit des fonctionnalités de recherche pour l’application.
* Suggestions : fournit des suggestions au fur et à mesure que l’utilisateur tape dans la barre de recherche.
* Recherche de document : recherche un document par ID afin de récupérer tout son contenu pour la page de détails.

## <a name="how-is-the-sample-organized"></a>Comment l’exemple est-il organisé ?

L’[exemple](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website) inclut les éléments suivants :

|Application|Objectif|GitHub<br>Référentiel<br>Location|
|--|--|--|
|Client|Application React (couche de présentation) pour afficher les livres, avec la recherche. Elle appelle l’application Azure Function. |[/search-website/src](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/src)|
|Serveur|Application Azure .NET Function (couche métier) - appelle l’API Recherche cognitive Azure à l’aide du SDK .NET |[/search-website/api](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/api)|
|Insertion en bloc|Fichier .NET pour créer l’index et y ajouter des documents.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Configurer votre environnement de développement

Installez les éléments suivants pour votre environnement de développement local. 

- [.NET 3](https://dotnet.microsoft.com/download/dotnet/5.0)  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes
    - [Ressources Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Recherche cognitive Azure 0.2.0+](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure Static Web App](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Facultatif :
    - Ce tutoriel n’exécute pas l’API Azure Function localement mais si vous envisagez de le faire, vous devez installer [azure-functions-core-tools](../azure-functions/functions-run-local.md?tabs=linux%2ccsharp%2cbash#install-the-azure-functions-core-tools).

## <a name="fork-and-clone-the-search-sample-with-git"></a>Duplication et clonage de l’exemple de recherche avec git

La duplication de l’exemple de référentiel est essentielle pour pouvoir déployer l’application web statique. Les applications web déterminent les actions de génération et le contenu de déploiement en fonction de l’emplacement de votre duplication (fork) GitHub. L’exécution du code dans l’application web statique est distante. Azure Static Web Apps lit à partir du code présent dans votre exemple dupliqué.

1. Dans GitHub, dupliquez (fork) l’[exemple de référentiel](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

    Terminez le processus de duplication dans votre navigateur web avec votre compte GitHub. Ce tutoriel utilise votre duplication (fork) dans le cadre du déploiement vers une application web statique Azure. 

1. Sur un terminal bash, téléchargez l’exemple d’application sur votre ordinateur local. 

    Remplacez `YOUR-GITHUB-ALIAS` par votre alias GitHub. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-dotnet-samples
    ```

1. Dans Visual Studio Code, ouvrez votre dossier local du référentiel cloné. Les tâches restantes sont accomplies à partir de Visual Studio Code, sauf indication contraire.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Créez un groupe de ressources pour vos ressources Azure

1. Dans Visual Studio Code, ouvrez la [barre d’activités](https://code.visualstudio.com/docs/getstarted/userinterface) et sélectionnez l’icône Azure. 
1. Dans la barre latérale, **cliquez avec le bouton droit sur votre abonnement Azure** sous la zone `Resource Groups`, puis sélectionnez **Créer un groupe de ressources**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Dans la barre latérale, **cliquez avec le bouton droit sur votre abonnement Azure** sous la zone « Groupes de ressources », puis sélectionnez **Créer un groupe de ressources**.":::
1. Entrez un nom de groupe de ressources, par exemple `cognitive-search-website-tutorial`. 
1. Sélectionnez un emplacement proche de vous.
1. Plus loin dans ce tutoriel, au moment de créer les ressources Recherche cognitive et Application web statique, utilisez ce groupe de ressources. 

    La création d’un groupe de ressources vous donne une unité logique pour gérer les ressources, notamment les supprimer une fois que vous avez fini de les utiliser.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un index de recherche et le charger avec des documents](tutorial-csharp-create-load-index.md)
* [Déployer votre application web statique](tutorial-csharp-deploy-static-web-app.md)