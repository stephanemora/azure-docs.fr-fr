---
title: Traitement de texte et d'images intégré pendant l'indexation
titleSuffix: Azure Cognitive Search
description: Les compétences pour l’extraction de données, le langage naturel et le traitement des images renforcent la sémantique et la structure du contenu brut dans un pipeline d’enrichissement Recherche cognitive Azure.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 161813b7da42a5697fdb595e6a7050923e28fca2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038890"
---
# <a name="built-in-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Compétences intégrées pour le traitement de texte et d’images pendant l’indexation (Recherche cognitive Azure)

Cet article décrit les compétences fournies avec Recherche cognitive Azure et que vous pouvez inclure dans un [ensemble de compétences](cognitive-search-working-with-skillsets.md) afin d’extraire le contenu et la structure de texte et fichiers images non structurés bruts. Une *compétence* est une opération atomique qui transforme du contenu d’une certaine façon. Il s’agit souvent d’une opération de reconnaissance ou d’extraction de texte, mais il peut aussi s’agir d’une compétence utilitaire qui remodèle les enrichissements déjà créés. En règle générale, la sortie est basée sur du texte afin de pouvoir être utilisée dans les requêtes de texte intégral. 

## <a name="built-in-skills"></a>Compétences prédéfinies

Les compétences prédéfinies sont basées sur des modèles préformés de Microsoft, ce qui signifie que vous ne pouvez pas effectuer l’apprentissage du modèle à l’aide de vos propres données d’apprentissage. Les compétences qui appellent les API de ressources cognitives ont une dépendance sur ces services et sont facturées au tarif du paiement à l’accès des Cognitive Services lorsque vous [associez une ressource](cognitive-search-attach-cognitive-services.md). Les autres compétences sont mesurées par Azure Recherche cognitive ou sont des compétences utilitaires qui sont disponibles gratuitement.

Le tableau suivant énumère et décrit les compétences intégrées.

| Type OData  | Description | Mesuré par |
|-------|-------------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md) | Recherche du texte dans une liste personnalisée définie par l’utilisateur de mots et d’expressions.| Recherche cognitive Azure ([tarifs](https://azure.microsoft.com/pricing/details/search/)) |
| [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | Cette compétence utilise un modèle préformé pour détecter des phrases importantes en fonction de la position du terme, des règles linguistiques, de la proximité d’autres termes et du caractère inhabituel du terme dans la source de données. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Cette compétence utilise un modèle préformé pour détecter la langue utilisée (un ID de langue par document). Quand plusieurs langues sont utilisées dans les mêmes segments de texte, la sortie est le LCID de la langue utilisée principalement. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolide en un champ unique du texte issu d’une collection de champs.  | Non applicable |
| [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md) | Cette compétence utilise un modèle préentraîné pour générer des liens pour les entités reconnues vers des articles de Wikipédia. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) | Cette compétence utilise un modèle préformé pour établir des entités pour un ensemble fixe de catégories : champs `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"DateTime"`, `"URL"`, `"Email"`, `"PersonType"`, `"Event"`, `"Product"`, `"Skill"`, `"Address"`, `"Phone Number"` et `"IP Address"`. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Cette compétence utilise un modèle préformé pour extraire des informations personnelles d’un texte donné. Elle offre également différentes options pour masquer les entités d’informations personnelles détectées dans le texte.  | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)  | Cette compétence utilise un modèle préformé pour affecter des étiquettes de sentiment (comme « négatif », « neutre » et « positif ») en fonction du score de confiance le plus élevé trouvé par le service au niveau du document et de la phrase enregistrement par enregistrement. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Fractionne le texte en pages afin que vous puissiez enrichir ou augmenter le contenu de façon incrémentielle. | Non applicable |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Cette qualification utilise un modèle préformé pour traduire le texte d’entrée en plusieurs langues pour les cas d’usage de normalisation ou de localisation. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Cette compétence utilise un algorithme de détection d’image pour identifier le contenu d’une image et générer un texte descriptif. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconnaissance optique de caractères. | Cognitive Services ([tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/)) |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Permet le filtrage, l’attribution d’une valeur par défaut et la fusion de données selon une condition. | Non applicable |
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrait le contenu d’un fichier dans le pipeline d’enrichissement. | Recherche cognitive Azure ([tarifs](https://azure.microsoft.com/pricing/details/search/))
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mappe la sortie sur un type complexe (type de données de plusieurs parties, qui peut être utilisé pour un nom complet, une adresse sur plusieurs lignes ou une combinaison d’un nom et d’un identificateur personnel.) | Non applicable |

## <a name="custom-skills"></a>Compétences personnalisées

Les [compétences personnalisées](cognitive-search-custom-skill-web-api.md) sont des modules que vous concevez, développez et déployez sur le Web. Vous pouvez ensuite appeler le module à partir d’un ensemble de compétences en tant que compétence personnalisée.

| Type  | Description | Mesuré par |
|-------|-------------|-------------|
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permet l’extensibilité du pipeline d’enrichissement de l’intelligence artificielle en passant un appel HTTP dans une API web personnalisée | Aucun, sauf si votre solution utilise un service Azure mesuré |
| [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md) | Permet l’extensibilité d’un pipeline d’enrichissement d’intelligence artificielle avec un modèle Azure Machine Learning | Aucun, sauf si votre solution utilise un service Azure mesuré |

Pour obtenir des conseils sur la création d’une compétence personnalisée, consultez [Définir une interface personnalisée](cognitive-search-custom-skill-interface.md) et [Exemple : création d’une compétence personnalisée pour l’enrichissement par IA](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Voir aussi

+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Définition d’une interface de compétences personnalisée](cognitive-search-custom-skill-interface.md)
+ [Tutoriel : Indexation enrichie avec l’IA](cognitive-search-tutorial-blob.md)
