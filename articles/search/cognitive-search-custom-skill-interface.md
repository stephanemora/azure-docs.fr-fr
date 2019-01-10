---
title: Définition d’interface pour des compétences personnalisées dans une recherche cognitive - Recherche Azure
description: Interface d’extraction de données personnalisée pour compétence personnalisée d’API web dans un pipeline de recherche cognitive dans Recherche Azure
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: deb72bcc41e20057b6e7b214c6a8c93655894a12
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628270"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Comment ajouter une compétence personnalisée à un pipeline de recherche cognitive

Un [pipeline d’indexation de recherche cognitive](cognitive-search-concept-intro.md) dans Recherche Azure peut être assemblé à partir de [compétences prédéfinies](cognitive-search-predefined-skills.md), ainsi que des compétences personnalisées que vous créez et ajoutez au pipeline. Dans cet article, découvrez comment créer une compétence personnalisée qui présente une interface lui permettant de faire partie d’un pipeline de recherche cognitive. 

Construire une compétence personnalisée vous donne un moyen d’insérer des transformations uniques dans votre contenu. Une compétence personnalisée s’exécute de façon indépendante, en appliquant l’étape d’enrichissement dont vous avez besoin. Par exemple, vous pouvez définir des entités personnalisées spécifiques de votre domaine, créer des modèles de classification personnalisés pour différencier des contrats et documents commerciaux et financiers, ou ajouter une compétence de reconnaissance vocale pour explorer plus profondément des fichiers audio afin d’en extraire du contenu pertinent. Pour un exemple pas à pas, voir [Exemple : création d’une compétence personnalisée](cognitive-search-create-custom-skill-example.md).

 Quelle que soit la capacité personnalisée dont vous avez besoin, il existe une interface simple et claire pour connecter une compétence personnalisée au reste du pipeline d’enrichissement. La seule exigence pour l’inclusion d’une capacité dans un [jeu de compétences](cognitive-search-defining-skillset.md) est la possibilité d’accepter des entrées et de générer des sorties de manières exploitables dans le jeu de compétences dans son ensemble. Cet article se concentre sur les formats d’entrée et de sortie que le pipeline d’enrichissement exige.

## <a name="web-api-custom-skill-interface"></a>Interface de compétence personnalisée d’API web

Les critères d’évaluation des compétences WebAPI personnalisées doivent renvoyer une réponse dans un délai de 5 minutes. Le pipeline d’indexation est synchrone et l’indexation produit une erreur de délai d’expiration lorsqu’une réponse n’est pas reçue dans ce délai.

Actuellement, le seul mécanisme d’interaction avec une compétence personnalisée est l’interface d’API web. L’API web doit satisfaire les exigences décrites dans cette section.

### <a name="1--web-api-input-format"></a>1.  Format d’entrée de l’API web

L’API web doit accepter un tableau d’enregistrements à traiter. Chaque enregistrement doit contenir un « jeu de propriétés » qui est l’entrée fournie à votre API web. 

Supposons que vous souhaitez créer un enrichisseur simple qui identifie la première date mentionnée dans le texte d’un contrat. Dans cet exemple, la compétence accepte une entrée unique, *contractText*, en tant que texte du contrat. La compétence a également une sortie unique, qui est la date du contrat. Pour rendre l’enrichisseur plus intéressant, retournez cette *date de contrat* (contractDate) sous la forme d’un type complexe à parties multiples.

Votre API web doit être prête à recevoir un lot d’enregistrements d’entrée. Chaque élément du tableau *valeurs* représente l’entrée d’un enregistrement particulier. Chaque enregistrement doit comporter les éléments suivants :

+ Un membre *recordId* qui est l’identificateur unique d’un enregistrement particulier. Quand votre enrichisseur retourne les résultats, il doit fournir ce *recordId* afin de permettre à l’appelant de faire correspondre les résultats de l’enregistrement à leur entrée.

+ Un membre *date* qui est essentiellement un jeu de champs d’entrée pour chaque enregistrement.

Pour être plus concret, sur la base de l’exemple ci-dessus, votre API web doit attendre des demandes ressemblant à ceci :

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
En réalité, votre service peut être appelé avec des centaines voire des milliers d’enregistrements au lieu de seulement les trois présentés ici.

### <a name="2-web-api-output-format"></a>2. Format de sortie de l’API web

Le format de la sortie est un ensemble d’enregistrements contenant un *recordId* et un jeu de propriétés. 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Cet exemple particulier ne génère qu’une seule sortie, mais vous pouvez générer plus d’une propriété. 

### <a name="errors-and-warning"></a>Erreurs et avertissements

Comme le montre l’exemple précédent, vous pouvez renvoyer des messages d’erreur et d’avertissement pour chaque enregistrement.

## <a name="consuming-custom-skills-from-skillset"></a>Utilisation des compétences personnalisées du jeu de compétences

Lorsque vous créez un enrichisseur d’API web, vous pouvez décrire des en-têtes HTTP et des paramètres dans le cadre de la demande. L’extrait de code ci-dessous montre comment des paramètres de requête et des en-têtes HTTP peuvent être décrits dans le cadre de la définition d’un jeu de compétences.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

+ [Exemple : Création d’une compétence personnalisée pour l’API Traduire le texte](cognitive-search-create-custom-skill-example.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
