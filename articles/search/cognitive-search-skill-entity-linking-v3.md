---
title: Compétence cognitive Liaison d’entités
titleSuffix: Azure Cognitive Search
description: Extrayez différentes entités liées d’un texte dans un pipeline d’enrichissement dans Recherche cognitive Azure.
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: cda66e72dac7e2adde5dbf2a59e13db5a236d37d
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525433"
---
# <a name="entity-linking-cognitive-skill"></a>Compétence cognitive Liaison d’entités

La compétence **Liaison d’entités** extrait les entités liées du texte. Cette compétence utilise les modèles Machine Learning fournis par [Analyse de texte](../cognitive-services/text-analytics/overview.md) dans Cognitive Services.

> [!NOTE]
> Cette compétence est liée à Cognitive Services et nécessite une [ressource facturable](cognitive-search-attach-cognitive-services.md) pour les transactions qui dépassent 20 documents par indexeur et par jour. L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.EntityLinkingSkill

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement doit être de 50 000 caractères telle que mesurée par [`String.Length`](/dotnet/api/system.string.length). Si vous devez subdiviser vos données avant de les envoyer à la compétence Liaison d’entités, songez utiliser la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Paramètres de la compétence

Les noms de paramètres sont sensibles à la casse et sont tous facultatifs.

| Nom du paramètre     | Description |
|--------------------|-------------|
| `defaultLanguageCode` |    Code de langue du texte d’entrée. Si le code de langue par défaut n’est pas spécifié, l’anglais (en) est utilisé comme code de langue par défaut. <br/> Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/language-support.md). |
| `minimumPrecision` | Valeur comprise entre 0 et 1 Si le score de confiance (dans la sortie `entities`) est inférieur à cette valeur, l’entité n’est pas retournée. La valeur par défaut est 0. |
| `modelVersion` | (Facultatif) Version du modèle à utiliser lors de l’appel du service Analyse de texte. Si rien n’est spécifié, la dernière version disponible est utilisée par défaut. Nous vous recommandons de ne pas spécifier cette valeur, sauf nécessité absolue. Pour plus d’informations, consultez [Contrôle de version de modèle dans l’API Analyse de texte](../cognitive-services/text-analytics/concepts/model-versioning.md).|


## <a name="skill-inputs"></a>Entrées de la compétence

| Nom d’entrée      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | Chaîne indiquant la langue des enregistrements. Si ce paramètre n’est pas spécifié, le code de langue par défaut est utilisé pour l’analyse des enregistrements. <br/>Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/language-support.md). |
| `text`          | Texte à analyser.          |

## <a name="skill-outputs"></a>Sorties de la compétence


| Nom de sortie      | Description                   |
|---------------|-------------------------------|
| `entities` | Tableau de types complexes contenant les champs suivants : <ul><li>name (nom réel de l’entité tel qu’il apparaît dans le texte)</li> <li>id </li> <li>language (langue du texte déterminée par la compétence)</li> <li>url (URL liée à cette entité)</li> <li>bingId (bingId pour cette entité liée)</li> <li>datasource (source de données associée à l’URL) </li> <li>matches (tableau de types complexes contenant : `text`, `offset`, `length` et `confidenceScore`)</li></ul>|


##    <a name="sample-definition"></a>Exemple de définition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
    "context": "/document",
    "defaultLanguageCode": "en", 
    "minimumPrecision": 0.5, 
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
            "name": "entities", 
            "targetName": "entities" 
        }
    ]
}
```
##    <a name="sample-input"></a>Exemple d’entrée

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft is liked by many.",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Exemple de sortie

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "entities": [
          {
            "name": "Microsoft", 
            "id": "Microsoft",
            "language": "en", 
            "url": "https://en.wikipedia.org/wiki/Microsoft", 
            "bingId": "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85", 
            "dataSource": "Wikipedia", 
            "matches": [
                {
                    "text": "Microsoft", 
                    "offset": 0, 
                    "length": 9, 
                    "confidenceScore": 0.13 
                }
            ]
          }
        ],
      }
    }
  ]
}
```

Notez que les décalages renvoyés pour les entités dans la sortie de cette qualification le sont directement à partir de l'[API Analyse de texte](../cognitive-services/text-analytics/overview.md), ce qui signifie que si vous les utilisez pour indexer dans la chaîne d'origine, vous devez utiliser la classe [StringInfo](/dotnet/api/system.globalization.stringinfo) dans .NET afin d'extraire le bon contenu.  [Pour plus d’informations, cliquez ici.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Cas d’avertissement
Si le code de langue du document n’est pas pris en charge, un avertissement est retourné et aucune entité n’est extraite.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)