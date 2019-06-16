---
title: Méthode Interpret - API Service d’exploration des connaissances
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser la méthode Interpret dans l’API Service d’exploration des connaissances (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 88776e2f4167c950d60c0405dcf950b5173fb989
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814139"
---
# <a name="interpret-method"></a>Méthode Interpret

La méthode *Interpret* extrait une chaîne de requête de langage naturel et retourne des interprétations formatées de l’intention de l’utilisateur sur les données de grammaire et d’index.  Pour fournir une expérience de recherche interactive, cette méthode peut être appelée à la saisie de chaque caractère par l’utilisateur avec le paramètre *complete* défini sur 1 pour activer la suggestion de saisie semi-automatique.

## <a name="request"></a>Requête

`http://<host>/interpret?query=<query>[&<options>]`

Nom|Valeur| Description
----|----|----
query    | Chaîne de texte | Requête saisie par l’utilisateur.  Si le paramètre complete est défini sur 1, la requête sera interprétée en tant que préfixe pour générer des suggestions de saisie semi-automatique pour la requête.        
terminé | 0 (valeur par défaut) ou 1 | 1 signifie que les suggestions de saisie semi-automatique sont générées en fonction des données de la grammaire et de l’index.         
count    | Nombre (par défaut = 10) | Nombre maximal d’interprétations à renvoyer.         
Offset   | Nombre (par défaut = 0) | Index de la première interprétation à renvoyer.  Par exemple, *count=2&offset=0* renvoie les interprétations 0 et 1. *count=2&offset=2* renvoie les interprétations 2 et 3.       
timeout  | Nombre (par défaut = 1 000) | Délai d'expiration en millisecondes. Seules les interprétations récupérées avant la fin du délai d'expiration sont renvoyées.

À l’aide des paramètres *count* et *offset*, un grand nombre de résultats peuvent être obtenus de façon incrémentielle sur plusieurs requêtes.

## <a name="response-json"></a>Réponse (JSON)

JSONPath     | Description
---------|---------
$.query |Paramètre *query* de la requête.
$.interpretations   |Tableau comportant 0 ou plusieurs façons de correspondre à la requête d’entrée sur la grammaire.
$.interpretations[\*].logprob   |Probabilité logarithmique relative de l’interprétation (<= 0).  Les grandes valeurs sont plus pertinentes.
$.interpretations[\*].parse |Chaîne XML illustrant la façon dont chaque partie de la requête est interprétée.
$.interpretations[\*].rules |Tableau comportant 1 ou plusieurs règles définies dans la grammaire et qui ont été appelées lors de l’interprétation.
$.interpretations[\*].rules[\*].name    |Nom de la règle.
$.interpretations[\*].rules[\*].output  |Résultat sémantique de la règle.
$.interpretations[\*].rules[\*].output.type |Type de données du résultat sémantique.
$.interpretations[\*].rules[\*].output.value|Valeur du résultat sémantique.  
$.aborted | True si la requête a expiré.

### <a name="parse-xml"></a>XML Parse

Le fichier XML Parse annote les requêtes (terminées) avec des informations sur leur correspondance avec les règles de grammaire et les attributs de l’index.  Voici un exemple du domaine des publications académiques :

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

L’élément `<rule>` délimite l’étendue dans la requête correspondant à la règle spécifiée par son attribut `name`.  Il peut être imbriqué lorsque l’analyse implique des références d’une règle de grammaire.

L’élément `<attr>` délimite l’étendue dans la requête correspondant à l’attribut de l’index spécifié par son attribut `name`.  Lorsque la correspondance comprend un synonyme dans la requête de sortie, l’attribut `canonical` contient la valeur canonique correspondant au synonyme de l’index.

## <a name="example"></a>Exemples

Dans l’exemple des publications académiques, la requête suivante renvoie jusqu'à 2 suggestions de saisie semi-automatique pour la requête de préfixe « papers by jaime » :

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

La réponse ci-dessous contient les deux interprétations les plus pertinentes (« count=2 ») qui complètent larequête partielle « papers by jaime »: « papers by jaime teevan » et « papers by jaime green ».  Le service a généré des suggestions de requête au lieu de prendre en compte uniquement les correspondances exactes de l’auteur « jaime », car la requête spécifiait « complete=1 ». Notez que la valeur canonique « j l green » a été mise en correspondance via le synonyme « jamie green », comme indiqué dans l’analyse.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Lorsque le type de résultat sémantique est « query », comme dans cet exemple, les objets correspondants peuvent être récupérés en envoyant *output.value* à la méthode d’API [*evaluate*](evaluateMethod.md) via le paramètre *expr*.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
