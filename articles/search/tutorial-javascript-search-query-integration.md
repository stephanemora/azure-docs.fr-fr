---
title: 'Tutoriel JavaScript : Principales caractéristiques de l’intégration de la recherche'
titleSuffix: Azure Cognitive Search
description: Comprenez les requêtes d’intégration de la recherche du SDK JavaScript utilisées dans le site web avec fonctionnalité de recherche grâce à cet aide-mémoire.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 3353ff11a93fa4a79c266b7bd323071b55cd76c6
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109848102"
---
# <a name="4---javascript-search-integration-cheat-sheet"></a>4 - Aide-mémoire de l’intégration de la recherche JavaScript

Dans les leçons précédentes, vous avez ajouté la recherche à une application web statique. Cette leçon met en évidence les étapes essentielles qui établissent l’intégration. Si vous recherchez une aide-mémoire sur l’intégration de la recherche dans votre application JavaScript, cet article explique ce que vous devez savoir.

L’application est disponible : 
* [Exemple](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Site web de démonstration - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="azure-sdk-azuresearch-documents"></a>Kit de développement logiciel (SDK) Azure @azure/search-documents 

L’application de fonction utilise le Kit de développement logiciel (SDK) Azure pour Recherche cognitive :

* NPM : [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Documentation de référence : [Bibliothèque de client](/javascript/api/overview/azure/search-documents-readme)

L’application de fonction s’authentifie via le Kit de développement logiciel (SDK) dans l’API Recherche cognitive basée sur le cloud à l’aide du nom de votre ressource, de la clé de la ressource et du nom de l’index. Les secrets sont stockés dans les paramètres de l’application web statique et extraits dans Azure Function en tant que variables d’environnement. 

## <a name="configure-secrets-in-a-configuration-file"></a>Configurer des secrets dans un fichier de configuration

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Azure Function : Rechercher dans le catalogue

L’[API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) `Search` prend un terme de recherche et effectue la recherche dans les documents de l’index de recherche, renvoyant une liste de correspondances. 

Le routage de l’API Search est contenu dans les liaisons [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json).

Azure Function extrait les informations de configuration de Search et accomplit la requête.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Client : recherche dans le catalogue

Appelez Azure Function dans le client React à l’aide du code suivant. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure Function : suggestions à partir du catalogue

L’[API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) `Suggest` prend un terme de recherche lors de la saisie et suggère des termes de recherche tels que des titres et des auteurs de livres dans les documents de l’index de recherche, renvoyant une petite liste de correspondances. 

Le générateur de suggestions de recherche, `sg`, est défini dans le [fichier de schéma](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) utilisé lors du chargement en bloc.

Le routage de l’API Suggest est contenu dans les liaisons [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json).

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Client : suggestions à partir du catalogue

L'API de fonction Suggest est appelée dans l'application React à l'emplacement `\src\components\SearchBar\SearchBar.js` dans le cadre de l'initialisation du composant :

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure Function : accéder à un document spécifique 

L’[API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) `Lookup` prend un ID et retourne l’objet de document à partir de l’index de recherche. 

Le routage de l’API Lookup est contenu dans les liaisons [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json).

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Client : accéder à un document spécifique 

Cette API de fonction est appelée dans l’application React à l’emplacement `\src\pages\Details\Detail.js` dans le cadre de l’initialisation du composant :

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Étapes suivantes

* [Indexer des données Azure SQL](search-indexer-tutorial.md)
