---
title: Compétences prédéfinies pour l’extraction de données, le langage naturel et le traitement des images (Recherche Azure) | Microsoft Docs
description: Les compétences cognitives pour l’extraction de données, le langage naturel et le traitement des images renforcent la sémantique et la structure du contenu brut dans un pipeline Recherche Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: c342eca8f27db713e139c187147abddd80eb854e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734532"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Compétences prédéfinies pour l’enrichissement de contenu (Recherche Azure)

Dans cet article, vous allez découvrir les compétences cognitives fournies avec Recherche Azure. Une *compétence cognitive* est une opération qui transforme du contenu d’une certaine façon. Souvent, il s’agit d’un composant qui extrait des données ou déduit une structure, renforçant ainsi notre compréhension des données d’entrée. Presque toujours, la sortie est basée sur du texte. Un *ensemble de compétences* est la collection des compétences qui définissent le pipeline d’enrichissement. 

> [!NOTE]
> La recherche cognitive est disponible en version préliminaire publique. L’exécution de l’ensemble de compétences ainsi que l’extraction et la normalisation d’images sont à l’heure actuelle proposées gratuitement. Le prix de ces fonctionnalités sera annoncé à une date ultérieure. 

## <a name="predefined-skills"></a>Compétences prédéfinies

Diverses compétences sont flexibles du point de vue de ce qu’elles consomment ou produisent. En règle générale, la plupart des compétences sont basées sur des modèles préformés, ce qui signifie que vous ne pouvez pas effectuer l’apprentissage du modèle à l’aide de vos propres données d’apprentissage. Pour obtenir des conseils sur la création d’une compétence personnalisée, consultez [Guide pratique pour définir une interface personnalisée](cognitive-search-custom-skill-interface.md) et [Exemple : création d’une compétence personnalisée](cognitive-search-create-custom-skill-example.md). Le tableau suivant énumère et décrit les compétences fournies par Microsoft. 

| Compétence | Description |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Cette compétence utilise un modèle préformé pour détecter des phrases importantes en fonction de la position du terme, des règles linguistiques, de la proximité d’autres termes et du caractère inhabituel du terme dans la source de données. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Cette compétence utilise un modèle préformé pour détecter la langue utilisée (un ID de langue par document). Quand plusieurs langues sont utilisées dans les mêmes segments de texte, la sortie est le LCID de la langue utilisée principalement.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Consolide en un champ unique du texte issu d’une collection de champs.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Cette compétence utilise un modèle préformé pour établir des entités pour un ensemble fixe de catégories : personnes, emplacement, organisation. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Cette compétence utilise un modèle préformé pour évaluer un sentiment positif ou négatif enregistrement par enregistrement. Le score est compris entre 0 et 1. Les scores neutres se produisent dans les cas Null où aucun sentiment n’est détectable et quand le texte est considéré comme neutre.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Fractionne le texte en pages afin que vous puissiez enrichir ou augmenter le contenu de façon incrémentielle. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Cette compétence utilise un algorithme de détection d’image pour identifier le contenu d’une image et générer un texte descriptif. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconnaissance optique de caractères. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mappe la sortie sur un type complexe (type de données de plusieurs parties, qui peut être utilisé pour un nom complet, une adresse sur plusieurs lignes ou une combinaison d’un nom et d’un identificateur personnel.) |

## <a name="see-also"></a>Voir aussi

+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Définition d’une interface de compétences personnalisée](cognitive-search-custom-skill-interface.md)
+ [Tutoriel : indexation enrichie avec la recherche cognitive](cognitive-search-tutorial-blob.md)