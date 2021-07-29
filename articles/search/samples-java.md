---
title: Exemples relatifs à Java
titleSuffix: Azure Cognitive Search
description: Recherchez des exemples de code Java de démonstration pour Recherche cognitive Azure, qui utilisent le Kit de développement logiciel (SDK) Azure .NET pour Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 39547b3405e25faabef64b6a9c80d0405fe1e66a
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110586387"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Exemples de code Java pour Recherche cognitive Azure

Découvrez les exemples de code Java qui illustrent les fonctionnalités et le flux de travail d’une solution Recherche cognitive Azure. Ils utilisent la [**bibliothèque de client Recherche cognitive Azure**](/java/api/overview/azure/search-documents-readme) pour le [**kit de développement logiciel (SDK) Azure pour Java**](/azure/developer/java/sdk), que vous pouvez explorer à l’aide des liens suivants.

| Cible | Lien |
|--------|------|
| Téléchargement de packages | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| Informations de référence sur l'API | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| Cas de test d’API | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Code source | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)

Les exemples de code de l’équipe de développement du kit SDK Azure illustrent l’utilisation des API. Vous les trouverez dans [**Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) sur GitHub.

| Exemples | Description |
|---------|-------------|
| [Création d’index de recherche](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Montre comment créer des [index de recherche](search-what-is-an-index.md). |
| [Création de synonyme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Montre comment créer des [cartes de synonymes](search-synonyms.md).  |
| [Créer un indexeur de recherche](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Montre comment créer des [indexeurs](search-indexer-overview.md). |
| [Création de source de données d’indexeur de recherche](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Montre comment créer des sources de données d’indexeur requises pour l’indexation basée sur un indexeur de [sources de données Azure prises en charge](search-indexer-overview.md#supported-data-sources). |
| [Création d’ensemble de compétences](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Montre comment créer des [ensembles de compétences](cognitive-search-working-with-skillsets.md) qui sont des indexeurs attachés, et qui effectuent un enrichissement basé sur l’intelligence artificielle pendant l’indexation. |
| [Chargement de documents](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Montre comment charger ou fusionner des documents dans un index dans le cadre d’une opération d’[importation de données](search-what-is-data-import.md). |
| [Syntaxe de requête](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Montre comment configurer une [requête de base](search-query-overview.md). |

## <a name="doc-samples"></a>Exemples de documentation

Les exemples de code de l’équipe Recherche cognitive illustrent les fonctionnalités et les flux de travail. La plupart sont utilisés dans des tutoriels, des guides de démarrage rapide et des articles pratiques. Vous trouverez ces exemples dans [**Azure-Samples/azure-search-java-samples**](https://github.com/Azure-Samples/azure-search-java-samples) sur GitHub.

| Exemples | Article | 
|---------|-------------|
| [démarrage rapide](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Code source pour [Démarrage rapide : Création d’un index de recherche en Java et REST](search-get-started-java.md). Cet exemple n’a pas été mis à jour pour le kit SDK Java. Il appelle les API REST. |

> [!Tip]
> Essayez le [navigateur d’exemples](/samples/browse/?languages=java&products=azure-cognitive-search) pour rechercher des exemples de code Microsoft dans GitHub, filtrés par produit, par service et par langage.

## <a name="other-samples"></a>Autres exemples

Les exemples suivants sont également publiés par l’équipe Recherche cognitive, mais la documentation n’y fait pas référence. Les fichiers Lisez-moi associés indiquent les instructions d’utilisation.

| Exemples | Description |
|---------|-------------|
| [search-java-getting-started](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Utilise la bibliothèque de client du SDK Java pour créer, charger et interroger un index de recherche. Cet exemple est actuellement autonome. |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Montre un indexeur Azure Cosmos DB en Java. Cet exemple n’a pas été mis à jour pour le kit SDK Java. Il appelle les API REST.|
