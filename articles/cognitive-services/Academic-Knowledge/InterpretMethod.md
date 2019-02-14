---
title: Méthode Interpret – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Utilisez la méthode Interpret pour renvoyer des interprétations formatées des chaînes de requête des utilisateurs en fonction des données Academic Graph et Academic Grammar de Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6db9a5b65fc2723af2eae006ad81716e23e52133
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860522"
---
# <a name="interpret-method"></a>Méthode Interpret

L’API REST **interpret** considère la chaîne de requête d’un utilisateur final (par exemple, une requête saisie par un utilisateur de votre application) et renvoie des interprétations formatées de l’intention de l’utilisateur en fonction des données Academic Graph et Academic Grammar.

Pour offrir une expérience interactive, vous pouvez appeler cette méthode de façon répétée après chaque caractère saisi par l’utilisateur. Dans ce cas, vous devez définir le paramètre **complete** sur 1 pour activer les suggestions de saisie semi-automatique. Si la saisie semi-automatique n’est pas utile pour votre application, vous devez définir le paramètre **complete** sur 0.

**Point de terminaison REST :**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Paramètres de la requête

Nom     | Valeur | Requis ?  | Description
---------|---------|---------|---------
**query**    | Chaîne de texte | OUI | Requête saisie par l’utilisateur.  Si le paramètre complete est défini sur 1, la requête sera interprétée en tant que préfixe pour générer des suggestions de saisie semi-automatique pour la requête.        
**model**    | Chaîne de texte | Non   | Nom du modèle que vous souhaitez interroger.  Actuellement, la valeur par défaut est définie sur *lastest* (plus récent).        
**complete** | 0 ou 1 | Non <br>par défaut : 0  | 1 signifie que les suggestions de saisie semi-automatique sont générées en fonction des données de la grammaire et du graphique.         
**count**    | Number | Non <br>par défaut :10 | Nombre maximal d’interprétations à renvoyer.         
**offset**   | Number | Non <br>par défaut : 0  | Index de la première interprétation à renvoyer. Par exemple, *count=2&offset=0* renvoie les interprétations 0 et 1. *count=2&offset=2* renvoie les interprétations 2 et 3.       
**timeout**  | Number | Non <br>par défaut : 1000 | Délai d’expiration en millisecondes. Seules les interprétations récupérées avant la fin du délai d’expiration sont renvoyées.
<br>
  
## <a name="response-json"></a>Réponse (JSON)
Nom     | Description
---------|---------
**query** |Paramètre *query* de la requête.
**interpretations** |Tableau comportant 0 ou plusieurs méthodes différentes pour mettre en correspondance la saisie de l’utilisateur avec la grammaire.
**interpretations[x].logprob**  |Probabilité logarithmique naturelle relative de l’interprétation. Les grandes valeurs sont plus pertinentes.
**interpretations[x].parse**  |Chaîne XML illustrant la façon dont chaque partie de la requête est interprétée.
**interpretations[x].rules**  |Tableau comportant 1 ou plusieurs règles définies dans la grammaire et qui ont été appelées lors de l’interprétation. L’API Connaissances universitaires comporte toujours au moins 1 règle.
**interpretations[x].rules[y].name**  |Nom de la règle.
**interpretations[x].rules[y].output**  |Résultat de la règle.
**interpretations[x].rules[y].output.type** |Type de données du résultat de la règle.  L’API Connaissances universitaires renvoie toujours « query ».
**interpretations[x].rules[y].output.value**  |Résultat de la règle. L’API Connaissances universitaires est toujours associée à une chaîne d’expression de requête qui peut être transmise aux méthodes evaluate et calchistogram.
**aborted** | True si la requête a expiré.

<br>
#### <a name="example"></a>Exemple :
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>La réponse ci-dessous contient les deux interprétations les plus pertinentes (en raison du paramètre *count=2*) qui complètent la saisie partielle de l’utilisateur *papers by jaime*: *papers by jaime teevan*  et *papers by jaime green*.  Le service a généré des suggestions de requête au lieu de prendre en compte uniquement les correspondances exactes de l’auteur *jaime*, car la requête spécifiait *complete=1*. Notez que la valeur canonique *j l green* a été mise en correspondance via le synonyme *jamie green*, comme indiqué dans l’analyse.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Pour récupérer les résultats d’entités pour une interprétation, utilisez *output.value* à partir de l’API **interpret** et transmettez le résultat à l’API **evaluate** via le paramètre *expr*. Dans cet exemple, la requête pour la première interprétation est : 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
