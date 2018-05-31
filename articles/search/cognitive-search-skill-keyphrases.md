---
title: Compétence de recherche cognitive Extraction de phrases clés (Recherche Azure) | Microsoft Docs
description: Évalue un texte non structuré puis, pour chaque enregistrement, retourne une liste de phrases clés dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a12efaa020e626e4a10a0708c9b84d8fe125588c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786808"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Compétence cognitive Extraction de phrases clés

La compétence **Extraction de phrases clés** évalue un texte non structuré puis, pour chaque enregistrement, retourne une liste de phrases clés.

Cette fonctionnalité est utile si vous avez besoin d’identifier rapidement les principaux points de discours dans l’enregistrement. Par exemple, si nous considérons le texte d’entrée « la nourriture était délicieuse et le personnel était merveilleux », le service retourne « nourriture » et « personnel merveilleux ».

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement doit être de 50 000 caractères telle que mesurée par `String.Length`. Si vous devez subdiviser vos données avant de les envoyer à l’extracteur de phrases clés, envisagez d’utiliser la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.
| Entrées                | Description |
|---------------------|-------------|
| defaultLanguageCode | (Facultatif) Code de langue à appliquer aux documents qui ne spécifient pas explicitement une langue.  Si le code de langue par défaut n’est pas spécifié, l’anglais (en) est utilisé comme code de langue par défaut. <br/> Consultez la [liste complète des langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Facultatif) Nombre maximal de phrases clés à produire. |

## <a name="skill-inputs"></a>Entrées de la compétence
| Entrées     | Description |
|--------------------|-------------|
| text | Texte à analyser.|
| languageCode  |  Chaîne indiquant la langue des enregistrements. Si ce paramètre n’est pas spécifié, le code de langue par défaut est utilisé pour l’analyse des enregistrements. <br/>Consultez la [liste complète des langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).|

##  <a name="sample-definition"></a>Exemple de définition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Exemple de sortie

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Erreurs et avertissements
Si vous indiquez un code de langue non pris en charge, une erreur est générée et les phrases clés ne sont pas extraites.
Si votre texte est vide, un avertissement est généré.
Si votre texte comprend plus de 50 000 caractères, seuls les 50 000 premiers caractères sont analysés et un avertissement est émis.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)