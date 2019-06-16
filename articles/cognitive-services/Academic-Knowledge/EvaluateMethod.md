---
title: Méthode Evaluate - API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Utilisez la méthode Evaluate pour renvoyer un ensemble d’entités universitaires basées sur une expression de requête.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: d2e628fb7fc502ef9ba81d20680d66f24fd7d138
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339084"
---
# <a name="evaluate-method"></a>Méthode Evaluate

L’API REST **evaluate** est utilisée pour renvoyer un ensemble d’entités universitaires en fonction d’une expression de requête.
<br>

**Point de terminaison REST :**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Paramètres de la requête  

Nom     | Valeur | Requis ?  | Description
-----------|-----------|---------|--------
**expr**       | Chaîne de texte | OUI | Expression de requête qui spécifie les entités à renvoyer.
**model**      | Chaîne de texte | Non  | Nom du modèle que vous souhaitez interroger.  Actuellement, la valeur par défaut est définie sur *lastest* (plus récent).        
**attributes** | Chaîne de texte | Non<br>par défaut : ID | Liste délimitée par des virgules qui spécifie les valeurs d’attribut qui sont incluses dans la réponse. Les noms d’attribut sont sensibles à la casse.
**count**        | Number | Non<br>Valeur par défaut : 10 | Nombre de résultats à renvoyer.
**offset**     | Number |   Non<br>Valeur par défaut : 0    | Index du premier résultat à renvoyer.
**orderby** |   Chaîne de texte | Non<br>Par défaut : probabilité décroissante | Nom d’un attribut utilisé pour trier les entités. Si vous le souhaitez, vous pouvez indiquer un ordre croissant ou décroissant. Le format est : *name:asc* ou *name:desc*.
  
 <br>

## <a name="response-json"></a>Réponse (JSON)

Nom | Description
-------|-----   
**expr** |  Paramètre *expr* issu de la requête.
**entities** |  Tableau comportant 0 ou plusieurs entités correspondant à l’expression de requête. Chaque entité contient une valeur de probabilité logarithmique naturelle et les valeurs des autres attributs requis.
**aborted** | True si la requête a expiré.

<br>

#### <a name="example"></a>Exemple :
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>En règle générale, une expression sera obtenue à partir d’une réponse à la méthode **interpret**.  Cependant, vous pouvez également composer des expressions de requête vous-même (consultez la page [Syntaxe des expressions de requête](QueryExpressionSyntax.md)).  
  
Vous pouvez utiliser les paramètres *count* et *offset* pour obtenir un grand nombre de résultats sans avoir à envoyer une requête unique pouvant entraîner une réponse très volumineuse (et potentiellement lente).  Dans cet exemple, la requête utilise l’expression pour la première interprétation à partir de la réponse d’API **interpret** en tant que valeur *expr*. Le paramètre *count=2* indique que 2 résultats d’entité sont requis. Le paramètre *attributes=Ti,Y,CC,AA.AuN,AA.AuId* indique que le titre, l’année, le nombre de citations, le nom de l’auteur et l’ID de l’auteur sont requis pour chaque résultat.  Consultez la page [Attributs d’entité](EntityAttributes.md) pour connaître la liste des attributs.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
