---
title: 'Tutoriel .NET : Principales caractéristiques de l’intégration de la recherche'
titleSuffix: Azure Cognitive Search
description: Découvrez les requêtes d’intégration de recherche du SDK .NET utilisées dans le site web avec fonctionnalité de recherche grâce à cet aide-mémoire.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: 504116f9bbed178889cd2e6d370c0c912da0ad00
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107981943"
---
# <a name="4---net-search-integration-cheat-sheet"></a>4 - Aide-mémoire de l’intégration de recherche .NET

Dans les leçons précédentes, vous avez ajouté la recherche à une application web statique. Cette leçon met en évidence les étapes essentielles qui établissent l’intégration. Si vous recherchez un aide-mémoire sur l’intégration de la recherche dans votre application web, cet article explique ce que vous devez savoir.

L’application est disponible : 
* [Exemple](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website)
* [Site web de démonstration - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="azure-sdk-azuresearchdocuments"></a>Azure.Search.Documents du SDK Azure

L’application de fonction utilise le Kit de développement logiciel (SDK) Azure pour Recherche cognitive :

* NuGet : [Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
* Documentation de référence : [Bibliothèque de client](/dotnet/api/overview/azure/search)

L’application de fonction s’authentifie via le Kit de développement logiciel (SDK) dans l’API Recherche cognitive basée sur le cloud à l’aide du nom de votre ressource, de la clé de la ressource et du nom de l’index. Les secrets sont stockés dans les paramètres de l’application web statique et extraits dans Azure Function en tant que variables d’environnement. 

## <a name="configure-secrets-in-a-localsettingsjson-file"></a>Configurer des secrets dans un fichier local.settings.json

1. Créez un fichier nommé `local.settings.json` dans `./api/` et copiez l’objet JSON suivant dans le fichier.

    ```json
    {
      "IsEncrypted": false,
      "Values": {
        "AzureWebJobsStorage": "",
        "FUNCTIONS_WORKER_RUNTIME": "dotnet",
        "SearchApiKey": "YOUR_SEARCH_QUERY_KEY",
        "SearchServiceName": "YOUR_SEARCH_RESOURCE_NAME",
        "SearchIndexName": "good-books"
      }
    }
    ```

1. Remplacez les valeurs suivantes par vos propres valeurs de ressource de recherche : 
    * YOUR_SEARCH_RESOURCE_NAME
    * YOUR_SEARCH_QUERY_KEY

## <a name="azure-function-search-the-catalog"></a>Azure Function : Rechercher dans le catalogue

L’[API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Search.cs) `Search` prend un terme de recherche et effectue la recherche dans les documents de l’index de recherche, renvoyant une liste de correspondances. 

Azure Function extrait les informations de configuration de Search et accomplit la requête.

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Search.cs" highlight="22-24, 55" :::

## <a name="client-search-from-the-catalog"></a>Client : recherche dans le catalogue

Appelez Azure Function dans le client React à l’aide du code suivant. 

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure Function : suggestions à partir du catalogue

L’[API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Suggest.cs) `Suggest` prend un terme de recherche lors de la saisie et suggère des termes de recherche tels que des titres et des auteurs de livres dans les documents de l’index de recherche, renvoyant une petite liste de correspondances. 

Le générateur de suggestions de recherche, `sg`, est défini dans le [fichier de schéma](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/bulk-insert/BookSearchIndex.cs) utilisé lors du chargement en bloc.

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Suggest.cs" highlight="21-23, 50-52" :::

## <a name="client-suggestions-from-the-catalog"></a>Client : suggestions à partir du catalogue

L’API de fonction Suggest est appelée dans l’application React à l’emplacement `\src\components\SearchBar\SearchBar.js` dans le cadre de l’initialisation du composant :

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure Function : accéder à un document spécifique 

L’[API](https://github.com/Azure-Samples/azure-search-dotent-samples/blob/master/search-website/api/Lookup.cs) `Lookup` prend un ID et retourne l’objet de document à partir de l’index de recherche. 

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Lookup.cs" highlight="19-21, 42" :::

## <a name="client-get-specific-document"></a>Client : accéder à un document spécifique 

Cette API de fonction est appelée dans l’application React à l’emplacement `\src\pages\Details\Detail.js` dans le cadre de l’initialisation du composant :

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="c-models-to-support-function-app"></a>Modèles C# pour prendre en charge l’application de fonction

Les modèles suivants sont utilisés pour prendre en charge les fonctions dans cette application.

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Models.cs" :::

## <a name="next-steps"></a>Étapes suivantes

* [Indexer des données Azure SQL](search-indexer-tutorial.md)
