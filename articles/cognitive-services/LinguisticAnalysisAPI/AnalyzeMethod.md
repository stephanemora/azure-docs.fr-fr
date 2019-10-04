---
title: Méthode d'analyse - API Analyse linguistique
titlesuffix: Azure Cognitive Services
description: Comment utiliser la méthode d’analyse dans l’API Analyse linguistique pour analyser certaines entrées en langage naturel.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 02c41e2510fd77f4bb65143faf62737f0985d2b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61401183"
---
# <a name="analyze-method"></a>Méthode d’analyse

> [!IMPORTANT]
> La préversion de l’analyse linguistique a été désactivée le 9 août 2018. Nous vous recommandons d’utiliser les [modules d’analyse de texte Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) pour le traitement et l’analyse de texte.

L’API REST **analyse**est utilisée pour analyser une entrée donnée en langage naturel.
Cela implique simplement la recherche de [phrases et jetons](Sentences-and-Tokens.md) dans cette entrée, la recherche de [balises morphosyntaxiques](POS-tagging.md), ou la recherche de l’[arbre de circonscription](Constituency-Parsing.md).
Vous pouvez spécifier les résultats que vous souhaitez obtenir en choisissant les analyseurs appropriés.
Pour répertorier tous les analyseurs disponibles, consultez les **[analyseurs](AnalyzersMethod.md)** .

Notez que vous devez spécifier la langue de la chaîne d’entrée.

**Point de terminaison REST :**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Paramètres de la demande

Nom | Type | Obligatoire | Description
-----|-------|----------|------------
**language**    | chaîne | OUI | Les deux lettres du code de langue ISO à utiliser pour l’analyse Par exemple, le code de langue anglais est « en ».
**analyzerIds** | liste de valeurs string | OUI | Liste des GUID des analyseurs à appliquer. Pour plus d’informations, consultez la documentation Analyseurs.
**text**        | chaîne | OUI | Entrée brute à analyser. Il peut s’agir d’une chaîne courte comme un mot ou une expression, une phrase complète, un paragraphe entier ou un discours.

## <a name="response-json"></a>Réponse (JSON)

Un tableau des sorties de l’analyse, une pour chaque attribut spécifié dans la requête.

Les résultats se présentent comme suit :

Nom | type | Description
-----|------|--------------
analyzerId | chaîne | GUID de l’analyseur spécifié
result | objet | résultat de l’analyseur

Notez que le type de résultat varie selon le type de l’analyseur d’entrée.

### <a name="tokens-response-json"></a>Réponse des jetons (JSON)

Nom | type | Description
-----|------|-------------
result | liste des objets de la phrase | limites de longueur de phrase identifiées dans le texte |
result[x].Offset | int | décalage du caractère de début à chaque phrase |
result[x].Len | int | longueur de chaque phrase en caractères |
result[x].Tokens | liste des objets de jeton | limites de longueur de jeton identifiées dans la phrase |
result[x].Tokens[y].Offset | int | décalage du caractère de début du jeton |
result[x].Tokens[y].Len | int | longueur du jeton en caractères |
result[x].Tokens[y].RawToken | chaîne | les caractères à l’intérieur du jeton, avant la normalisation |
result[x].Tokens[y].NormalizedToken | chaîne | une forme normalisée du caractère, à utiliser dans [l’arborescence d’analyse](Constituency-Parsing.md) ; par exemple, une parenthèse ouvrante « ( » devient « -LRB- » |

Exemple d’entrée : « il s’agit d’un test. Hello. »
Exemple de réponse JSON :
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>Réponse de balises PDV (JSON)

Le résultat est une liste de listes de chaînes.
Pour chaque phrase, il existe une liste de balises PDV, une balise PDV pour chaque jeton.
Pour rechercher le jeton correspondant à chaque balise PDV, demandez également un objet de segmentation du texte en unités lexicales.

### <a name="constituency-tree-response-json"></a>Réponse de l’arbre de circonscription (JSON)

Le résultat est une liste de chaînes, une arborescence d’analyse pour chaque phrase trouvée dans l’entrée.
Les arborescences d’analyse sont représentées entre parenthèses.

## <a name="example"></a>Exemples

`POST /analyze`

Corps de la demande : Charge utile JSON
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Réponse : JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
