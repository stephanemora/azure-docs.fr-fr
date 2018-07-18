---
title: Méthode Evaluate dans l’API Service d’exploration des connaissances | Microsoft Docs
description: Découvrez comment utiliser la méthode Evaluate dans l’API Service d’exploration des connaissances (KES) dans Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368029"
---
# <a name="evaluate-method"></a>Méthode evaluate
La méthode *evaluate* évalue et renvoie le résultat d’une expression de requête structurée en fonction de données d’index.

En règle générale, une expression sera obtenue à partir d’une réponse à la méthode interpret.  Cependant, vous pouvez également composer des expressions de requête vous-même (consultez la page [Structured Query Expression](Expressions.md) (Expressions de requête structurée)).  

## <a name="request"></a>Requête 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

NOM|Valeur|Description
----|----|----
expr       | Chaîne de texte | Expression de requête structurée qui sélectionne un sous-ensemble d’entités d’index.
attributs | Chaîne de texte | Liste d’attributs séparés par des virgules à inclure dans la réponse.
count      | Nombre (par défaut = 10) | Nombre maximal de résultats à renvoyer.
Offset     | Nombre (par défaut = 0) | Index du premier résultat à renvoyer.
orderby |   Chaîne de texte | Nom de l’attribut utilisé pour trier les résultats, suivis d’un ordre de tri facultatif (par défaut = asc) : « *attrname*[ : (asc&#124;desc)] ».  Si l’attribut n’est pas spécifié, les résultats sont renvoyés dans l’ordre décroissant de la probabilité logarithmique naturelle.
timeout  | Nombre (par défaut = 1 000) | Délai d'expiration en millisecondes. Seuls les résultats calculés avant la fin du délai d'expiration sont renvoyés.

À l’aide des paramètres *count* et *offset*, un grand nombre de résultats peuvent être obtenus de façon incrémentielle sur plusieurs requêtes.
  
## <a name="response-json"></a>Réponse (JSON)
JSONPath|Description
----|----
$.expr | Paramètre *expr* issu de la requête.
$.entities | Tableau de 0 ou plusieurs entités d’objet correspondant à l’expression de requête structurée. 
$.aborted | True si la requête a expiré.

Chaque entité contient une valeur *logprob* et les valeurs des attributs demandés.

## <a name="example"></a>Exemples
Dans l’exemple des publications académiques, la requête suivante transmet une expression de requête structurée (potentiellement depuis la sortie d’une requête *interpret*) et récupère quelques attributs pour les 2 meilleures entités correspondantes :

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

La réponse contient les 2 meilleures entités correspondantes les plus probables (« count=2 »).  Pour chaque entité, les attributs de titre, d’année, de nom de l’auteur et d’ID de l’auteur sont renvoyés.  Notez comment la structure des valeurs d’attributs composites correspond à la façon dont elles sont indiquées dans le fichier de données. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
