---
title: Méthode CalcHistogram dans l’API Connaissances universitaires | Microsoft Docs
description: Utilisez la méthode CalcHistogram pour calculer la distribution des valeurs d’attribut pour un ensemble d’entités de publication dans Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e0b773fb9791ee638c8cfdbbc9dca40543e50ec0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367872"
---
# <a name="calchistogram-method"></a>Méthode CalcHistogram

L’API REST **calchistogram** est utilisée pour calculer la distribution des valeurs d’attribut pour un ensemble d’entités de publication.          


**Point de terminaison REST :**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>Paramètres de la requête

NOM  |Valeur | Requis ?  |Description
-----------|----------|--------|----------
**expr**    |Chaîne de texte | OUI  |Expression de requête qui spécifie les entités sur lesquelles calculer les histogrammes.
**model** |Chaîne de texte | Non  |Sélectionnez le nom du modèle que vous souhaitez interroger.  Actuellement, la valeur par défaut est définie sur *lastest* (plus récent).
**attributes** | Chaîne de texte | Non <br>par défaut : | Liste délimitée par des virgules qui spécifie les valeurs d’attribut qui sont incluses dans la réponse. Les noms d’attribut sont sensibles à la casse.
**count** |Number | Non <br>Valeur par défaut : 10 |Nombre de résultats à renvoyer.
**offset**  |Number | Non <br>Par défaut : 0 |Index du premier résultat à renvoyer.
<br>
## <a name="response-json"></a>Réponse (JSON)
NOM | Description
--------|---------
**expr**  |Paramètre expr issu de la requête.
**num_entities** | Nombre total d’entités correspondantes.
**histograms** |  Tableau d’histogrammes (un pour chaque attribut spécifié dans la requête).
**histograms[x].attribute** | Nom de l’attribut sur lequel l’histogramme a été calculé.
**histograms[x].distinct_values** | Nombre de valeurs différentes parmi les entités correspondantes pour cet attribut.
**histograms[x].total_count** | Nombre total d’instances de valeur parmi les entités correspondantes pour cet attribut.
**histograms[x].histogram** | Données d’histogramme pour cet attribut.
**histograms[x].histogram[y].value** |  Valeur pour l’attribut.
**histograms[x].histogram[y].logprob**  |Probabilité logarithmique naturelle totale d’entités correspondantes avec cette valeur d’attribut.
**histograms[x].histogram[y].count**  |Nombre d’entités correspondantes avec cette valeur d’attribut.
**aborted** | True si la requête a expiré.

 <br>
#### <a name="example"></a>Exemple :
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Dans cet exemple, pour générer un histogramme du nombre de publications par année pour un auteur particulier depuis 2010, nous pouvons tout d’abord générer l’expression de requête à l’aide de l’API **interpret** et de la chaîne de requête : *papers by jaime teevan after 2012* (publications de jaime teevan après 2012).

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>L’expression de la première interprétation renvoyée par l’API interpret est *And(Composite(AA.AuN=='jaime teevan'),Y>2012)*.
<br>Cette valeur de l’expression est ensuite transmise à l’API **calchistogram**. Le paramètre *attributes=Y,F.FN* indique que les distributions des nombres de publications doivent se faire par année et par champ d’étude, par exemple :
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>La réponse à cette requête indique tout d’abord que 37 publications correspondent à l’expression de requête.  Pour l’attribut *Year*, nous obtenons 3 valeurs distinctes, une pour chaque année après 2012 (c’est-à-dire 2013, 2014 et 2015), tel que spécifié dans la requête.  Le nombre total de publications sur les 3 différentes valeurs est de 37.  Pour chaque année *Year*, l’histogramme affiche la valeur, la probabilité logarithmique naturelle totale et le nombre d’entités correspondantes.     

L’histogramme du champ d’étude *Field of Study* indique qu’il y a 34 champs d’étude distincts. Une publication peut être associée à plusieurs champs d’étude et le nombre total (53) peut être supérieur au nombre d’entités correspondantes.  Bien que nous obtenions 34 valeurs distinctes, la réponse n’inclut que les 4 premières à cause du paramètre *count=4*.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
