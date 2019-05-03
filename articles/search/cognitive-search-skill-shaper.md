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
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5267f81c9886e2d1d8d62c134156aedb3b2b8763
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023711"
---
#   <a name="shaper-cognitive-skill"></a>Compétence cognitive Modélisation

Le **modélisateur** compétence consolide plusieurs entrées dans un [type complexe](search-howto-complex-data-types.md) qui peut être référencé plus loin dans le pipeline d’enrichissement. La compétence **Modélisation** vous permet essentiellement de créer une structure, de définir le nom des membres de cette structure et d’assigner des valeurs à chaque membre. Exemples de champs consolidées utiles dans les scénarios de recherche incluent l’association d’un prénom et le nom en une structure unique, la ville et l’état dans une structure unique, ou le nom et date de naissance dans une structure unique pour établir une identité unique.

La version d’API détermine la profondeur de mise en forme vous pouvez obtenir. 

| Version de l'API | Mise en forme des comportements | 
|-------------|-------------------|
| Version 2019-05-06-preview de l’API REST (Kit de développement logiciel .NET n’est pas pris en charge) | Objets complexes, plusieurs niveaux, dans un **modélisateur** définition de compétences. |
| 2019-05-06 ** 2017-11-11-Preview (la disposition générale)| Objets complexes, un niveau de profondeur. Une forme à plusieurs niveaux requiert le chaînage de plusieurs étapes de mise en forme.|

La version préliminaire **modélisateur** compétence, illustré dans [scénario 3](#nested-complex-types), ajoute un nouveau facultatif *sourceContext* propriété à l’entrée. Le *source* et *sourceContext* propriétés s’excluent mutuellement. Si l’entrée est dans le contexte de la compétence, utilisez simplement *source*. Si l’entrée est à un *différents* contexte que le contexte de la compétence, utilisez le *sourceContext*. Le *sourceContext* , vous devez définir une entrée imbriquée avec l’élément spécifique qui est traité comme source. 

Dans la réponse, pour toutes les versions d’API, le nom de sortie est toujours « output ». En interne, le pipeline peut mapper un nom différent, tel que « analyzedText » comme indiqué dans les exemples ci-dessous, mais la **modélisateur** compétence lui-même retourne « output » dans la réponse. Cet aspect peut être important si vous effectuez un débogage de documents enrichis et notez la différence de nommage, ou si vous générez une compétence personnalisée et que vous structurez la réponse vous-même.

> [!NOTE]
> Le **modélisateur** compétence n’est pas lié à une API Cognitive Services et vous n’êtes pas facturé pour l’utiliser. Toutefois, vous devez toujours [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) pour remplacer l’option de ressource **Gratuit** qui vous limite à un petit nombre d’enrichissements quotidiens par jour.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scénario 1 : les types complexes

Considérez un scénario dans lequel vous souhaitez créer une structure appelée *analyzedText* dotée de deux membres : *text* et *sentiment*. Dans un index Azure Search, un champ de recherche de plusieurs partie est appelé un *type complexe* et il est souvent créé lors de la source de données a une structure complexe correspondante qui mappe à ce dernier.

Toutefois, une autre approche pour la création de types complexes est via le **modélisateur** compétence. En incluant cette compétence dans les compétences, les opérations en mémoire pendant le traitement de compétences peuvent sortir des formes de données avec des structures imbriquées, qui peuvent alors être mappés à un type complexe dans votre index. 

La définition de compétence d’exemple suivante fournit les noms de membre comme entrée. 


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
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Index des exemples

Compétences est appelée par un indexeur, et un indexeur nécessite un index. Une représentation sous forme de champ complexes dans votre index peut se présenter comme dans l’exemple suivant. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Entrée de compétence

Un document JSON entrant en fournissant des données exploitables pour ce **modélisateur** compétences pourraient être :

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


### <a name="skill-output"></a>Sortie de compétence

La compétence **Modélisation** génère un nouvel élément appelé *analyzedText* avec les éléments combinés *text* et *sentiment*. Cette sortie est conforme au schéma d’index. Il est importé et indexé dans un index Azure Search.

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

## <a name="scenario-2-input-consolidation"></a>Scénario 2 : consolidation d’entrée

Dans un autre exemple, imaginez qu’à différents stades du traitement du pipeline, vous avez extrait le titre d’un livre et des titres de chapitre sur des pages différentes du livre. Vous pouvez maintenant créer une structure unique composée de ces différentes entrées.

Le **modélisateur** définition des compétences pour ce scénario peut se présenter comme dans l’exemple suivant :

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
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Sortie de compétence
Dans ce cas, le **modélisateur** aplatit tous les titres de chapitres pour créer un seul tableau. 

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

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scénario 3 : de consolidation d’entrée à partir de contextes imbriquées

> [!NOTE]
> Imbriqué des structures de prise en charge dans la version d’api = 2019-05-06-Preview peut être utilisé dans un [store de la base de connaissances](knowledge-store-concept-intro.md) ou dans un index Azure Search.

Imaginons que vous avez le titre, des chapitres et contenu d’un livre et avez été exécutées entité phrases de reconnaissance et la clé sur le contenu et devez maintenant pour agréger les résultats des compétences différentes dans une seule forme avec le nom de chapitre, les entités et les expressions clés.

Le **modélisateur** définition des compétences pour ce scénario peut se présenter comme dans l’exemple suivant :

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
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Sortie de compétence
Dans ce cas, le **modélisateur** crée un type complexe. Cette structure existe en mémoire. Si vous souhaitez enregistrer dans un magasin de la base de connaissances, vous devez créer une projection dans vos compétences qui définit les caractéristiques de stockage.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
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
+ [Comment utiliser des types complexes](search-howto-complex-data-types.md)
+ [Vue d’ensemble du magasin de base de connaissances](knowledge-store-concept-intro.md)
+ [La prise en main avec Store de la base de connaissances](knowledge-store-howto.md)