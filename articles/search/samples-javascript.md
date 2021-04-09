---
title: Exemples JavaScript
titleSuffix: Azure Cognitive Search
description: Recherchez des exemples de code JavaScript de démonstration pour Recherche cognitive Azure, qui utilisent le Kit de développement logiciel (SDK) Azure .NET pour JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 85a4d6390087100d8d9521f6ac20dbace3a711eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955939"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Exemples de code JavaScript pour Recherche cognitive Azure

Découvrez les exemples de code JavaScript qui illustrent les fonctionnalités et le flux de travail d’une solution Recherche cognitive Azure. Ils utilisent la [**bibliothèque de client Recherche cognitive Azure**](/javascript/api/overview/azure/search-documents-readme) pour le [**kit de développement logiciel (SDK) Azure pour JavaScript**](/azure/developer/javascript/), que vous pouvez explorer à l’aide des liens suivants.

| Cible | Lien |
|--------|------|
| Téléchargement de package | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| Informations de référence sur l'API | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| Cas de test d’API | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Code source | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)

Les exemples de code de l’équipe de développement du kit SDK Azure illustrent l’utilisation de l’API. Vous les trouverez dans [**azure-sdk-for-js/tree/master/sdk/search/search-documents/samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) sur GitHub.

### <a name="javascript-sdk-samples"></a>Exemples du Kit de développement logiciel (SDK) JavaScript

| Exemples | Description |
|---------|-------------|
| [index](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Montre comment créer, mettre à jour, extraire, répertorier et supprimer des [index de recherche](search-what-is-an-index.md). Cet exemple de catégorie comprend également un exemple de statistique de service. |
| [dataSourceConnections (pour les indexeurs)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des sources de données d’indexeur, nécessaires pour l’indexation basée sur un indexeur de [sources de données Azure prises en charge](search-indexer-overview.md#supported-data-sources). |
| [indexeurs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Montre comment créer, mettre à jour, obtenir, répertorier, réinitialiser et supprimer des [indexeurs](search-indexer-overview.md).|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des [ensembles de compétences](cognitive-search-working-with-skillsets.md) qui sont des indexeurs attachés, et qui effectuent un enrichissement basé sur l’intelligence artificielle pendant l’indexation. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des [cartes de synonymes](search-synonyms.md).  |

### <a name="typescript-samples"></a>Exemples TypeScript

| Exemples | Description |
|---------|-------------|
| [index](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Montre comment créer, mettre à jour, extraire, répertorier et supprimer des [index de recherche](search-what-is-an-index.md). Cet exemple de catégorie comprend également un exemple de statistique de service. |
| [dataSourceConnections (pour les indexeurs)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des sources de données d’indexeur, nécessaires pour l’indexation basée sur un indexeur de [sources de données Azure prises en charge](search-indexer-overview.md#supported-data-sources). |
| [indexeurs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Montre comment créer, mettre à jour, obtenir, répertorier, réinitialiser et supprimer des [indexeurs](search-indexer-overview.md).|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des [ensembles de compétences](cognitive-search-working-with-skillsets.md) qui sont des indexeurs attachés, et qui effectuent un enrichissement basé sur l’intelligence artificielle pendant l’indexation. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des [cartes de synonymes](search-synonyms.md).  |

## <a name="doc-samples"></a>Exemples de documentation

Les exemples de code de l’équipe Recherche cognitive illustrent les fonctionnalités et les flux de travail. La plupart sont référencés dans des tutoriels, démarrages rapides et articles pratiques. Vous trouverez ces exemples dans [**Azure-Samples/azure-search-javascript-samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) sur GitHub.

| Exemples | Article |
|---------|---------|
| [démarrage rapide](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Code source pour [Démarrage rapide : Créer un index de recherche en JavaScript](search-get-started-javascript.md). Cet article traite du flux de travail de base pour la création, le chargement et l’interrogation d’un index de recherche à l’aide d’exemples de données. |

> [!Tip]
> Essayez le [navigateur d’exemples](/samples/browse/?languages=javascript&products=azure-cognitive-search) pour rechercher des exemples de code Microsoft dans GitHub, filtrés par produit, par service et par langage.

## <a name="other-samples"></a>Autres exemples

Les exemples suivants sont également publiés par l’équipe Recherche cognitive, mais la documentation n’y fait pas référence. Les fichiers Lisez-moi associés indiquent les instructions d’utilisation.

| Exemples | Description |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Modèle React pour Recherche cognitive Azure (github.com) |