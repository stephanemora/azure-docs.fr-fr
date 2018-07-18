---
title: Méthode d’analyseurs dans l’API Analyse linguistique | Microsoft Docs
description: L’API REST d’analyseurs fournit une liste des analyseurs qui sont actuellement pris en charge par le service dans Microsoft Cognitive Services.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367896"
---
# <a name="analyzers-method"></a>Méthode d’analyseurs

L’API REST d’**analyseurs** fournit une liste des analyseurs qui sont actuellement pris en charge par le service.
La réponse inclut leurs [noms](Analyzer-Names.md) et les langues prises en charge par chacun d’eux (par exemple, « en » pour l’anglais).

## <a name="request-parameters"></a>Paramètres de la demande
Aucun

<br>

## <a name="response-parameters"></a>Paramètres de réponse
NOM | type | Description
-----|------|--------------
languages | liste de valeurs string | liste des codes de langue ISO à deux lettres pour lesquels cet analyseur peut être utilisé.
id   | chaîne | ID unique de cet analyseur
kind | chaîne | type général de l’analyseur
specification | chaîne | nom de la spécification utilisée pour cet analyseur
implementation | chaîne | description du modèle et/ou de l’algorithme derrière cet analyseur

<br>
## <a name="example"></a>Exemples
GET /analyzers

Réponse : JSON
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
