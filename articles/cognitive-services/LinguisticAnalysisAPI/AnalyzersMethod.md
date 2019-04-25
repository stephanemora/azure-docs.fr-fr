---
title: Méthode Analyseurs – API Analyse linguistique
titlesuffix: Azure Cognitive Services
description: L’API REST Analyseurs fournit la liste des analyseurs actuellement pris en charge par l’API Analyse linguistique.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404713"
---
# <a name="analyzers-method"></a>Méthode d’analyseurs

> [!IMPORTANT]
> La préversion de l’analyse linguistique a été désactivée le 9 août 2018. Nous vous recommandons d’utiliser les [modules d’analyse de texte Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) pour le traitement et l’analyse de texte.

L’API REST d’**analyseurs** fournit une liste des analyseurs qui sont actuellement pris en charge par le service.
La réponse inclut leurs [noms](Analyzer-Names.md) et les langues prises en charge par chacun d’eux (par exemple, « en » pour l’anglais).

## <a name="request-parameters"></a>Paramètres de la demande
Aucun

<br>

## <a name="response-parameters"></a>Paramètres de réponse

Name | type | Description
-----|------|--------------
languages | liste de valeurs string | liste des codes de langue ISO à deux lettres pour lesquels cet analyseur peut être utilisé.
id   | string | ID unique de cet analyseur
kind | string | type général de l’analyseur
specification | string | nom de la spécification utilisée pour cet analyseur
implementation | string | description du modèle et/ou de l’algorithme derrière cet analyseur

<br>

## <a name="example"></a>Exemples
GET /analyzers

Réponse : JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
