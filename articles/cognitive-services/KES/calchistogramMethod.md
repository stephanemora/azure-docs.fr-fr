---
title: Méthode CalcHistogram - API Service d’exploration des connaissances
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser la méthode CalcHistogram dans l’API Service d’exploration des connaissances (KES).
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: f994a254e661cd245d2e953efd3bd595d50c6fc7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214311"
---
# <a name="calchistogram-method"></a>Méthode calchistogram
La méthode *calchistogram* calcule les objets correspondant à une expression de requête structurée et calcule la distribution de leurs valeurs d’attribut.

## <a name="request"></a>Requête
`http://<host>/calchistogram?expr=<expr>[&options]` 

NOM|Valeur|Description
----|-----|-----------
expr | Chaîne de texte | Expression de requête structurée qui spécifie les entités d’index sur lesquelles calculer les histogrammes.
attributs | Chaîne de texte (par défaut = «») | Liste d’attributs séparés par des virgules à inclure dans la réponse.
count   | Nombre (par défaut = 10) | Nombre de résultats à renvoyer.
Offset  | Nombre (par défaut = 0) | Index du premier résultat à renvoyer.

## <a name="response-json"></a>Réponse (JSON)
JSONPath | Description
----|----
$.expr | Paramètre *expr* issu de la requête.
$.num_entities | Nombre total d’entités correspondantes.
$.histograms |  Tableau d’histogrammes, un pour chaque attribut demandé.
$.histograms[\*].attribute | Nom de l’attribut sur lequel l’histogramme a été calculé.
$.histograms[\*].distinct_values | Nombre de valeurs différentes parmi les entités correspondantes pour cet attribut.
$.histograms[\*].total_count | Nombre total d’instances de valeur parmi les entités correspondantes pour cet attribut.
$.histograms[\*].histogram | Données d’histogramme pour cet attribut.
$.histograms[\*].histogram[\*].value | Valeur de l’attribut.
$.histograms[\*].histogram[\*].logprob  | Probabilité logarithmique naturelle totale d’entités correspondantes avec cette valeur d’attribut.
$.histograms[\*].histogram[\*].count    | Nombre d’entités correspondantes avec cette valeur d’attribut.
$.aborted | True si la requête a expiré.

### <a name="example"></a>Exemples
Dans l’exemple des publications académiques, ce qui suit calcule un histogramme de nombres de publications par année et par mot clé pour un auteur particulier depuis 2013 :

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

La réponse indique qu’il n’y a que 37 documents qui correspondent à l’expression de requête.  Pour l’attribut *Année*, nous obtenons 3 valeurs distinctes, une pour chaque année depuis 2013.  Le nombre total de publications sur les 3 différentes valeurs est de 37.  Pour chaque *année*, l’histogramme affiche la valeur, la probabilité logarithmique naturelle totale et le nombre d’entités correspondantes.     

L’histogramme pour *Mot clé* montre qu’il existe 34 mots clés distincts. Un document pouvant être associé à plusieurs mots clés, le nombre total (53) peut être supérieur au nombre d’entités correspondantes.  Bien que nous obtenions 34 valeurs distinctes, la réponse n’inclut que les 4 premières à cause du paramètre count=4.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
