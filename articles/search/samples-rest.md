---
title: Exemples REST
titleSuffix: Azure Cognitive Search
description: Recherchez des exemples de code REST Recherche cognitive Azure qui utilisent les API REST Recherche ou Gestion.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956434"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Exemples de code Java pour Recherche cognitive Azure

Découvrez les exemples d’API REST qui illustrent les fonctionnalités et le flux de travail d’une solution Recherche cognitive Azure. Ces exemples utilisent les [**API REST Recherche**](/rest/api/searchservice).

REST est l’interface de programmation définitive pour Recherche cognitive Azure. Toutes les opérations qui peuvent être appelées par programme sont disponibles d’abord dans REST, puis dans les kits de développement logiciel (SDK). C’est pourquoi la plupart des exemples de la documentation tirent parti des API REST pour montrer ou expliquer des concepts importants.

Les exemples REST sont généralement développés et testés sur Postman, mais vous pouvez utiliser n’importe quel client prenant en charge les appels HTTP :

+ Commencez avec [Démarrage rapide : Créer un index Recherche cognitive Azure à l’aide d’API REST](search-get-started-rest.md) pour obtenir de l’aide concernant la formulation des appels HTTP.
+ Essayez l’[extension Visual Studio Code pour Recherche cognitive Azure](search-get-started-vs-code.md), actuellement en préversion, si vous utilisez Visual Studio Code.

## <a name="doc-samples"></a>Exemples de documentation

Les exemples de code de l’équipe Recherche cognitive illustrent les fonctionnalités et les flux de travail. La plupart sont référencés dans des tutoriels, démarrages rapides et articles de guide pratique. Vous trouverez ces exemples dans [**Azure-Samples/azure-search-postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples) sur GitHub.

| Exemples | Article |
|---------|---------|
| [Démarrage rapide](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Code source pour [Démarrage rapide : Créer un index de recherche à l’aide des API REST](search-get-started-rest.md). Cet article traite du flux de travail de base pour la création, le chargement et l’interrogation d’un index de recherche à l’aide d’exemples de données. |
| [Didacticiel](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Code source pour [Tutoriel : Utiliser REST et l’IA pour générer du contenu pouvant faire l’objet de recherches à partir d’objets blob Azure](cognitive-search-tutorial-blob.md). Cet article explique comment créer un ensemble de compétences qui effectuent une itération sur des blobs Azure pour extraire des informations et inférer une structure.|
| [Debug-sessions](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Code source pour [Tutoriel : Diagnostiquer, réparer et valider les changements apportés à votre ensemble de compétences](cognitive-search-tutorial-debug-sessions.md). Cet article montre comment utiliser une session de débogage d’ensemble compétences dans le portail Azure. REST est utilisé pour créer les objets utilisés lors du débogage.|
| [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Code source pour [Tutoriel : Créer un analyseur personnalisé pour les numéros de téléphone](tutorial-create-custom-analyzer.md). Cet article explique comment utiliser des analyseurs pour conserver des modèles et des caractères spéciaux dans du contenu pouvant faire l’objet d’une recherche.|
| [knowledge-store](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Code source pour [Créer une base de connaissances à l’aide de REST et Postman](knowledge-store-create-rest.md). Cet article explique les étapes nécessaires afin d’alimenter une base de connaissances utilisée pour des flux de travail d’exploration de connaissances. |
| [projections](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Code source pour [Guide pratique pour mettre en forme et exporter des enrichissements](knowledge-store-projections-examples.md). Cet article explique comment spécifier les structures de données physiques dans une base de connaissances.|
| [index-encrypted-blobs](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Code source pour [Indexer des objets blob chiffrés à l’aide d’indexeurs d’objets blob et d’ensembles de compétences](search-howto-index-encrypted-blobs.md). Cet article explique comment indexer dans le service Stockage Blob Azure des documents précédemment chiffrés à l’aide d’Azure Key Vault. |

> [!Tip]
> Essayez le [navigateur d’exemples](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) pour rechercher des exemples de code Microsoft dans GitHub, filtrés par produit, par service et par langage.

## <a name="other-samples"></a>Autres exemples

Les exemples suivants sont également publiés par l’équipe Recherche cognitive, mais ne sont pas référencés dans la documentation. Les fichiers Lisez-moi associés fournissent des instructions d’utilisation.

| Exemples | Description |
|---------|-------------|
| [Query-examples](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Collections Postman montrant les diverses techniques de requête, dont la recherche approximative, la recherche par expressions régulières et caractères génériques, l’autocomplétion, etc. |