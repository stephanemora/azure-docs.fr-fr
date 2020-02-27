---
title: Liens vers la documentation pour l’enrichissement de l’IA
titleSuffix: Azure Cognitive Search
description: Liste annotée d’articles, de tutoriels, d’exemples et de billets de blog sur les charges de travail d’enrichissement de l’IA dans la recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616190"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Ressources de documentation pour l’enrichissement de l’IA dans la recherche cognitive Azure

L’enrichissement de l’IA est une fonctionnalité de l’indexation de la recherche cognitive Azure qui trouve des informations latentes dans des sources non textuelles et le texte indifférencié, et les transforme en contenu avec possibilité de recherche en texte intégral dans la recherche cognitive Azure.

Les articles suivants constituent la documentation complète de l’enrichissement de l’IA.

## <a name="getting-started"></a>Prise en main
+ [Présentation de l’intelligence artificielle dans la Recherche cognitive Azure](cognitive-search-concept-intro.md)
+ [Démarrage rapide : Créer un ensemble de compétences cognitives dans le Portail Azure](cognitive-search-quickstart-blob.md)
+ [Tutoriel : Indexation enrichie avec l’IA](cognitive-search-tutorial-blob.md)
+ [Exemple : Création d’une compétence personnalisée pour l’enrichissement par l’IA](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Guides pratiques
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Guide pratique pour référencer des annotations dans un ensemble de compétences](cognitive-search-concept-annotations-syntax.md)
+ [Guide pratique pour mapper des champs sur un index](cognitive-search-output-field-mapping.md)
+ [Guide pratique pour traiter et extraire des informations à partir d’images](cognitive-search-concept-image-scenarios.md)
+ [Guide pratique pour régénérer un index de recherche cognitive Azure](search-howto-reindex.md)
+ [Guide pratique pour définir une interface de compétences personnalisées](cognitive-search-custom-skill-interface.md)
+ [Conseils de dépannage](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Informations de référence

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Compétences personnalisées
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Compétences dépréciées](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Créer un ensemble de compétences (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Créer un indexeur (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Voir aussi

+ [API REST Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md)
+ [Qu’est-ce que la recherche cognitive Azure ?](search-what-is-azure-search.md)
