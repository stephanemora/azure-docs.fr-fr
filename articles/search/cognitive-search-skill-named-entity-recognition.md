---
title: Compétence Reconnaissance d’entités nommées de la recherche cognitive (Recherche Azure) | Microsoft Docs
description: Extrait les entités nommées de personne, de lieu et d’organisation du texte dans un pipeline de recherche cognitive Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 653a4675d546432eea8478ba6203be1df71ec4f4
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731391"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Compétence cognitive Reconnaissance d’entités nommées

La compétence **Reconnaissance d’entités nommées** extrait les entités nommées du texte. Les types d’entités disponibles sont les suivants : `person`, `location` et `organization`.

> [!NOTE]
> La recherche cognitive est disponible en version préliminaire publique. L’exécution d’ensemble de compétences, ainsi que l’extraction et la normalisation d’images, sont actuellement proposées gratuitement. Le prix de ces fonctionnalités sera annoncé à une date ultérieure. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre     | Description |
|--------------------|-------------|
| Catégories    | Tableau des catégories à extraire.  Types de catégories possibles : `"Person"`, `"Location"`, `"Organization"`. Si aucune catégorie n’est précisée, tous les types sont retournés.|
|defaultLanguageCode |  Code de langue du texte d’entrée. Langues prises en charge : `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`.|
| minimumPrecision  | Nombre compris entre 0 et 1. Si la précision est inférieure à cette valeur, l’entité n’est pas retournée. La valeur par défaut est 0.|

## <a name="skill-inputs"></a>Entrées de la compétence

| Nom d’entrée      | Description                   |
|---------------|-------------------------------|
| languageCode  | facultatif. La valeur par défaut est `"en"`.  |
| texte          | Texte à analyser.          |

## <a name="skill-outputs"></a>Sorties de la compétence

| Nom de sortie     | Description                   |
|---------------|-------------------------------|
| persons      | Tableau de chaînes représentant chacune le nom d’une personne. |
| Emplacements  | Tableau de chaînes représentant chacune un lieu. |
| organizations  | Tableau de chaînes représentant chacune une organisation. |
| entities | Tableau de types complexes. Chaque type complexe contient les champs suivants : <ul><li>la catégorie (`"person"`, `"organization"` ou `"location"`) ;</li> <li>la valeur (le nom réel de l’entité) ;</li><li>le décalage (l’emplacement où elle a été trouvée dans le texte) ;</li><li>la confiance (une valeur comprise entre 0 et 1 représentant la confiance accordée à la valeur en tant qu’entité réelle).</li></ul> |

##  <a name="sample-definition"></a>Exemple de définition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
             "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Seattle"],
        "organizations":["Microsoft Corporation"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 45,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 59,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Seattle",
            "offset": 5,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft Corporation",
            "offset": 120,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Cas d’erreur
Si vous spécifiez un code de langue non pris en charge, ou si le contenu ne correspond pas à la langue spécifiée, une erreur est retournée et aucune entité n’est extraite.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)