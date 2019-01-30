---
title: Compétence de recherche cognitive Modélisation - Recherche Azure
description: Extrayez des métadonnées et des informations structurées à partir de données non structurées pour les modeler en type complexe dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a1f5a698ee76ebd0561bd19ff1a23d0f04be0771
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410113"
---
#   <a name="shaper-cognitive-skill"></a>Compétence cognitive Modélisation

La compétence **Modélisation** crée un type complexe pour prendre en charge les champs composites (ou champs multipartites). Un champ de type complexe comporte plusieurs parties, mais il est traité en tant qu’élément unique dans un index Recherche Azure. Parmi les exemples de champs regroupés utiles dans les scénarios de recherche, citons la combinaison d’un nom et d’un prénom dans un seul champ, d’une ville et d’un état dans un seul champ, ou du nom et de la date de naissance dans un seul champ pour établir une identité unique.

La compétence **Modélisation** vous permet essentiellement de créer une structure, de définir le nom des membres de cette structure et d’assigner des valeurs à chaque membre.

Par défaut, cette technique prend en charge les objets profonds d’un niveau. Pour les objets plus complexes, vous pouvez chaîner plusieurs étapes **Modélisation**.

Dans la réponse, le nom de sortie est toujours « output ». En interne, le pipeline peut mapper un autre nom, comme « analyzedText » dans les exemples ci-dessous, sur « output », mais la compétence **Modélisation** elle-même retourne « output » dans la réponse. Cet aspect peut être important si vous effectuez un débogage de documents enrichis et notez la différence de nommage, ou si vous générez une compétence personnalisée et que vous structurez la réponse vous-même.

> [!NOTE]
> Cette compétence n’est pas liée à une API Cognitive Services et son utilisation ne vous est pas facturée. Toutefois, vous devez toujours [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) pour remplacer l’option de ressource **Gratuit** qui vous limite à un petit nombre d’enrichissements quotidiens par jour.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Exemple 1 : types complexes

Considérez un scénario dans lequel vous souhaitez créer une structure appelée *analyzedText* dotée de deux membres : *text* et *sentiment*. Dans Recherche Azure, un champ multipartite pouvant faire l’objet d’une recherche est appelé *type complexe* et n’est pas encore pris en charge d’office. Dans cette préversion, une compétence **Modélisation** peut servir à générer des champs d’un type complexe dans votre index. 

L’exemple suivant fournit les noms de membre comme entrée. La structure de sortie (votre champ complexe dans Recherche Azure) est spécifiée par le biais de *targetName*. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Exemple d’entrée
Un document JSON fournissant des données d’entrée exploitables pour cette compétence **Modélisation** pourrait ressembler à ceci :

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="sample-output"></a>Exemple de sortie
La compétence **Modélisation** génère un nouvel élément appelé *analyzedText* avec les éléments combinés *text* et *sentiment*. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="sample-2-input-consolidation"></a>Exemple 2 : regroupement des entrées

Dans un autre exemple, imaginez qu’à différents stades du traitement du pipeline, vous avez extrait le titre d’un livre et des titres de chapitre sur des pages différentes du livre. Vous pouvez maintenant créer une structure unique composée de ces différentes entrées.

La définition de la compétence Modélisation pour ce scénario peut se présenter comme dans l’exemple suivant :

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Exemple de sortie
Dans ce cas, la compétence Modélisation aplatit tous les titres de chapitre en un tableau unique. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)

