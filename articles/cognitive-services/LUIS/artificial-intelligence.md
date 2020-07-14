---
title: Intelligence artificielle
description: LUIS utilise l’intelligence artificielle pour la compréhension du langage naturel de vos données, en fonction du schéma que vous avez défini.
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802636"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Intelligence artificielle dans Language Understanding (LUIS)

LUIS utilise l’intelligence artificielle pour la compréhension du langage naturel (NLU) de vos données, en fonction du schéma que vous avez défini.

## <a name="natural-language-processing"></a>Traitement en langage naturel

La compréhension du langage naturel (NLU) est une sous-rubrique spécifique du traitement en langage naturel (NLP).

Le traitement en langage naturel est un concept plus large qui gère toute forme de traitement des données textuelles, y compris les éléments suivants :

* Segmentation du texte en unités lexicales
* Balisage morphosyntaxique
* Segmentation
* Analyse morphologique
* Similarité sémantique
* Discourse
* Traduction

## <a name="natural-language-processing-in-luis"></a>Traitement en langage naturel dans LUIS

Le traitement en langage naturel est disponible dans votre application LUIS des manières suivantes :
* [Compréhension du langage naturel](#natural-language-understanding) (LUIS)
* Aspects du traitement en langage naturel configurables dans LUIS :
    * [Segmentation du texte en unités lexicales](luis-language-support.md#tokenization)
    * Morphologique à travers les signes diacritiques, la ponctuation et la forme des mots [Paramètres de l’API](luis-reference-application-settings.md)
* Avant ou après le traitement de l’énoncé de requête fourni par d’autres [Cognitive Services](../Welcome.md) comme :
    * [Traduction](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>Compréhension du langage naturel

NLU est la capacité à _transformer_ une déclaration linguistique en une représentation qui vous permet de comprendre naturellement vos utilisateurs. La compréhension du langage naturel reste un problème très compliqué et est définie comme un problème d’_IA-complet_.

LUIS est conçu pour se concentrer sur l’intention et l’extraction, cela comprend la possibilité d’identifier les éléments suivants :
* Ce que veulent les utilisateurs
* Ils en parlent.

LUIS a peu ou pas connaissance des aspects les plus larges du _NLP_, tels que la similarité sémantique, sans identification explicite dans des exemples. Par exemple, les jetons suivants (mots) sont trois éléments différents jusqu’à ce qu’ils soient utilisés dans des contextes similaires dans les exemples fournis :

* buy
* achat
* acheté

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [cycle de vie de développement](luis-concept-app-iteration.md) pour une application LUIS