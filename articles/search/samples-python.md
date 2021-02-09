---
title: Exemples Python
titleSuffix: Azure Cognitive Search
description: Recherchez des exemples de code Python de démonstration pour Recherche cognitive Azure, qui utilisent le Kit de développement logiciel (SDK) Azure .NET pour Python ou REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955120"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Exemples de code Python pour Recherche cognitive Azure

Découvrez les exemples de code Python qui illustrent les fonctionnalités et le flux de travail d’une solution Recherche cognitive Azure. Ils utilisent la [**Bibliothèque de client Recherche cognitive Azure**](/python/api/overview/azure/search-documents-readme) pour le [**kit SDK Azure pour Python**](/azure/developer/python/), que vous pouvez explorer à l’aide des liens suivants.

| Cible | Lien |
|--------|------|
| Téléchargement de package | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| Informations de référence sur l'API | [azure-search-documents](/python/api/azure-search-documents)  |
| Cas de test d’API | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Code source | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)

Les exemples de code de l’équipe de développement du kit SDK Azure illustrent l’utilisation de l’API. Vous les trouverez dans [**azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) sur GitHub.

| Exemples | Description |
|---------|-------------|
| [Authentifier](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Montre comment configurer un client et s’authentifier auprès du service. | 
| [Opérations de création, de lecture, de mise à jour et de suppression d’index](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Montre comment créer, mettre à jour, extraire, répertorier et supprimer des [index de recherche](search-what-is-an-index.md). |
| [Opérations de création, de lecture, de mise à jour et de suppression d’un indexeur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Montre comment créer, mettre à jour, obtenir, répertorier, réinitialiser et supprimer des [indexeurs](search-indexer-overview.md). |
| [Rechercher des sources de données d’indexeur](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des sources de données d’indexeur, nécessaires pour l’indexation basée sur un indexeur de [sources de données Azure prises en charge](search-indexer-overview.md#supported-data-sources). |
| [Synonymes](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Montre comment créer, mettre à jour, obtenir, répertorier et supprimer des [cartes de synonymes](search-synonyms.md).  |
| [Chargement de documents](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Montre comment charger ou fusionner des documents dans un index dans le cadre d’une opération d’[importation de données](search-what-is-data-import.md). |
| [Requête simple](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Montre comment configurer une [requête de base](search-query-overview.md). |
| [Requête de filtre](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Montre comment configurer une [expression de filtre](search-filters.md). |
| [Requête de facette](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Montre comment utiliser des [facettes](search-filters-facets.md). |

## <a name="doc-samples"></a>Exemples de documentation

Les exemples de code de l’équipe Recherche cognitive illustrent les fonctionnalités et les flux de travail. La plupart sont référencés dans des tutoriels, démarrages rapides et articles pratiques. Vous trouverez ces exemples dans [**Azure-Samples/azure-search-python-samples**](https://github.com/Azure-Samples/azure-search-python-samples) sur GitHub.

| Exemples | Article |
|---------|---------|
| [démarrage rapide](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Code source pour [Démarrage rapide : Créer un index de recherche en Python](search-get-started-python.md). Cet article traite du flux de travail de base pour la création, le chargement et l’interrogation d’un index de recherche à l’aide d’exemples de données. |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Code source pour [Tutoriel : Utiliser Python et l’IA pour générer du contenu pouvant faire l’objet de recherches à partir de blobs Azure](cognitive-search-tutorial-blob-python.md). Cet article explique comment créer un indexeur d’objets blob avec un ensemble de compétences cognitives, où l’ensemble de compétences crée et transforme du contenu brut pour le rendre détectable ou consommable. |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Code source pour [Exemple : Créer une compétence personnalisée avec Python](cognitive-search-custom-skill-python.md). Cet article illustre l’intégration de l’indexeur et de l’ensemble de compétences aux modèles de Deep Learning dans Azure Machine Learning. |

> [!Tip]
> Essayez le [navigateur d’exemples](/samples/browse/?languages=python&products=azure-cognitive-search) pour rechercher des exemples de code Microsoft dans GitHub, filtrés par produit, par service et par langage.