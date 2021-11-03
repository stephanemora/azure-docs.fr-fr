---
title: Compétence cognitive Fractionnement de texte
titleSuffix: Azure Cognitive Search
description: Découpe le texte en blocs ou des pages de texte en fonction de leur longueur dans un pipeline d’enrichissement par IA dans la Recherche cognitive Azure.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: e2531bcb010b83f795cc7f77b68848c86c4c470a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010666"
---
# <a name="text-split-cognitive-skill"></a>Compétence cognitive Fractionnement de texte

La compétence **Fractionnement de texte** découpe le texte en segments. Vous pouvez choisir de décomposer le texte en phrases ou en pages d’une longueur donnée. Cette opération est particulièrement utile si d’autres compétences en aval imposent des critères de longueur maximale du texte. 

> [!NOTE]
> Cette compétence n’est pas liée à Cognitive Services. Elle n’est pas facturable et aucune clé Cognitive Services n’est requise.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre     | Description |
|--------------------|-------------|
| `textSplitMode`    | `pages` ou `sentences` | 
| `maximumPageLength` | S’applique uniquement si `textSplitMode` est défini sur `pages`. Cela fait référence à la longueur de page maximale, en caractères, telle que mesurée par `String.Length`. La valeur minimale est 300, la valeur maximale 100000, et la valeur par défaut 10000.  L’algorithme s’efforce de scinder le texte en respectant les délimitations de phrases, de sorte que la taille de chaque bloc soit légèrement inférieure à `maximumPageLength`. | 
| `defaultLanguageCode` | (Facultatif) L’un des codes de langue suivants : `am, bs, cs, da, de, en, es, et, fr, he, hi, hr, hu, fi, id, is, it, ja, ko, lv, no, nl, pl, pt-PT, pt-BR, ru, sk, sl, sr, sv, tr, ur, zh-Hans`. La langue par défaut est l’anglais (en). Quelques points à prendre en compte :<ul><li>Il est utile d’indiquer un code de langue pour éviter de couper un mot en deux dans les langues sans espaces blancs, par exemple, le chinois, le japonais et le coréen.</li><li>Si vous ne connaissez pas la langue (par exemple, vous devez fractionner le texte pour l’entrée dans [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), la valeur par défaut de l’anglais (en) doit être suffisante. </li></ul>  |


## <a name="skill-inputs"></a>Entrées de la compétence

| Nom du paramètre       | Description      |
|----------------------|------------------|
| `text`    | Texte à fractionner en sous-chaînes. |
| `languageCode`    | (Facultatif) Code de langue du document. Si vous ne connaissez pas la langue (par exemple, vous devez fractionner le texte pour l’entrée dans [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), vous pouvez retirer cette entrée. Si la langue ne figure pas dans la liste prise en charge pour le paramètre `defaultLanguageCode` ci-dessus, un avertissement est émis et le texte n’est pas fractionné.  |

## <a name="skill-outputs"></a>Sorties de la compétence 

| Nom du paramètre     | Description |
|--------------------|-------------|
| `textItems`   | Tableau des sous-chaînes extraites. |


##  <a name="sample-definition"></a>Exemple de définition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Exemple d’entrée

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia...",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Exemple de sortie

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Cas d’erreur
Si la langue n’est pas prise en charge, un avertissement est généré.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
