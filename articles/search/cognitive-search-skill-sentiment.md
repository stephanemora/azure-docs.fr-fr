---
title: Compétence Sentiment de la recherche cognitive (Recherche Azure) | Microsoft Docs
description: Extrait le sentiment d’un texte dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786758"
---
#   <a name="sentiment-cognitive-skill"></a>Compétence cognitive Sentiment

La compétence **Sentiment** évalue du texte non structuré sur un continuum positif-négatif et, pour chaque enregistrement, retourne un score numérique compris entre 0 et 1. Un score proche de 1 indique un sentiment positif, et un score proche de 0 un sentiment négatif.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement est de 5 000 caractères selon `String.Length`. Si vous avez besoin de découper vos données avant de les envoyer à l’Analyseur des sentiments, utilisez la [compétence Fractionnement du texte](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre |                      |
|----------------|----------------------|
| defaultLanguageCode | (Facultatif) Code de langue à appliquer aux documents qui ne spécifient pas explicitement la langue. <br/> Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/text-analytics-supported-languages.md). |

## <a name="skill-inputs"></a>Entrées de la compétence 

| Nom d’entrée | Description |
|--------------------|-------------|
| text | Texte à analyser.|
| languageCode  |  (Facultatif) Chaîne indiquant la langue des enregistrements. Si ce paramètre n’est pas spécifié, la valeur par défaut est « en ». <br/>Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Sorties de la compétence

| Nom de sortie | Description |
|--------------------|-------------|
| de votre application | Valeur comprise entre 0 et 1 qui représente le sentiment du texte analysé. Les valeurs proches de 0 indiquent un sentiment négatif, les valeurs proches de 0,5 un sentiment neutre et les valeurs proches de 1 un sentiment positif.|


##  <a name="sample-definition"></a>Exemple de définition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Notes
Les scores de sentiment vides ne sont pas retournés pour ces enregistrements.

## <a name="error-cases"></a>Cas d’erreur
Si la langue n’est pas prise en charge, une erreur est générée et aucun score de sentiment n’est retourné.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)