---
title: Compétence Détection de la langue de la recherche cognitive (Recherche Azure) | Microsoft Docs
description: Évalue le texte non structuré et, pour chaque enregistrement, retourne un identificateur de langue avec un score indiquant la puissance de l’analyse dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 338d89b47ea451efcf8300d4ac016a6946a95259
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786838"
---
#   <a name="language-detection-cognitive-skill"></a>Compétence cognitive Détection de la langue

La compétence **Détection de la langue** détecte la langue du texte d’entrée parmi 120 langues et retourne un code de langue unique pour chaque document soumis dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse.

Cette fonctionnalité est particulièrement utile lorsqu’il est nécessaire d’indiquer la langue du texte en entrée dans d’autres compétences (par exemple, la [compétence Analyse de sentiment](cognitive-search-skill-sentiment.md) ou la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement est de 50 000 caractères selon `String.Length`. Si vous avez besoin de découper vos données avant de les envoyer à l’Analyseur des sentiments, vous pouvez utiliser la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Entrées de la compétence

Les paramètres respectent la casse.

| Entrées     | Description |
|--------------------|-------------|
| text | Texte à analyser.|

## <a name="skill-outputs"></a>Sorties de la compétence

| Nom de sortie    | Description |
|--------------------|-------------|
| languageCode | Code ISO 6391 de la langue identifiée. Exemple : « en ». |
| languageName | Nom de la langue. Exemple : « anglais ». |
| de votre application | Valeur comprise entre 0 et 1 correspondant à la probabilité que la langue soit correctement identifiée. Le score peut être inférieur à 1 si la phrase comporte plusieurs langues.  |

##  <a name="sample-definition"></a>Exemple de définition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
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
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Cas d’erreur
Si le texte est exprimé dans une langue non prise en charge, une erreur est générée et aucun identificateur de langue n’est retourné.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)