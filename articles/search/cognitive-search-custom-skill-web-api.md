---
title: Compétence de recherche cognitive personnalisée - Recherche Azure
description: Étendre les fonctionnalités des compétences de recherche cognitive en appelant des API web
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: e1ca8a5ce7b615ed8d84c91d8a0d72098c175c44
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672136"
---
# <a name="custom-web-api-skill"></a>Compétence API web personnalisée

La compétence **API web personnalisée** vous permet d’étendre la recherche cognitive en appelant un point de terminaison d’API web qui fournit des opérations personnalisées. Tout comme les compétences intégrées, une compétence **API web personnalisée** a des entrées et des sorties. Selon les entrées, votre API web reçoit une charge utile JSON pendant l’exécution de l’indexeur et génère une charge utile JSON en réponse, ainsi qu’un code d’état de réussite. La réponse est censée avoir les sorties spécifiées par votre compétence personnalisée. Toute autre réponse est considérée comme une erreur et aucun enrichissement n’est effectué.

La structure des charges utiles JSON est décrite plus bas dans ce document.

> [!NOTE]
> L’indexeur réessaie deux fois pour certains codes d’état HTTP standard retournés par l’API web. Ces codes d’état HTTP sont les suivants : 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre     | Description |
|--------------------|-------------|
| URI | URI de l’API web à laquelle la charge utile _JSON_ est envoyée. Seul le schéma d’URI **https** est autorisé |
| httpMethod | Méthode à utiliser pour envoyer la charge utile. Les méthodes autorisées sont `PUT` ou `POST` |
| httpHeaders | Collection de paires clé-valeur où les clés représentent les noms d’en-tête et les valeurs représentent les valeurs d’en-tête à envoyer à votre API web avec la charge utile. Les en-têtes suivants sont interdits dans cette collection : `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | (Facultatif) Si spécifié, indique le délai d’expiration pour le client http qui effectue l’appel d’API. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Par exemple, `PT60S` pour 60 secondes. S’il n’est pas défini, une valeur par défaut de 30 secondes est choisie. Le délai d’expiration peut être défini sur 90 secondes maximum et 1 seconde minimum. |
| batchSize | (Facultatif) Indique le nombre « d’enregistrements de données » (voir la structure de charge utile _JSON_ ci-dessous) à envoyer par appel d’API. S’il n’est pas défini, une valeur par défaut de 1000 est choisie. Nous vous recommandons d’utiliser ce paramètre pour avoir un compromis entre le débit d’indexation et la charge sur votre API |

## <a name="skill-inputs"></a>Entrées de la compétence

Il n’y a pas d’entrée « prédéfinie » pour cette compétence. Si vous choisissez comme entrées un ou plusieurs champs déjà disponibles au moment de l’exécution de cette compétence, la charge utile _JSON_ envoyée à l’API web a des champs différents.

## <a name="skill-outputs"></a>Sorties de la compétence

Il n’y a pas de sortie « prédéfinie » pour cette compétence. En fonction de la réponse envoyée par votre API web, ajoutez des champs de sortie à choisir dans la réponse _JSON_.


## <a name="sample-definition"></a>Exemple de définition

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Exemple de structure JSON d’entrée

Cette structure _JSON_ représente la charge utile à envoyer à votre API web.
Elle suit toujours ces contraintes :

* L’entité de niveau supérieur est appelée `values` et est un tableau d’objets. Le nombre de ces objets équivaut à `batchSize` au maximum
* Chaque objet dans le tableau `values` a
    * Une propriété `recordId` constituant une chaîne **unique**, utilisée pour identifier cet enregistrement.
    * Une propriété `data` constituant un objet _JSON_. Les champs de la propriété `data` correspondent aux « noms » spécifiés dans la section `inputs` de la définition de compétence. La valeur de ces champs provient de la `source` de ces champs (qui peut être un champ dans le document ou éventuellement une autre compétence)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Exemple de structure JSON de sortie

« output » correspond à la réponse renvoyée par votre API web. L’API web doit retourner uniquement une charge utile _JSON_ (vérifiée en examinant l’en-tête de réponse `Content-Type`) et doit satisfaire les contraintes suivantes :

* Il doit y avoir une entité de niveau supérieur appelée `values` qui doit être un tableau d’objets.
* Le nombre d’objets dans le tableau doit être le même que le nombre d’objets envoyés à l’API web.
* Chaque objet doit avoir :
   * Une propriété `recordId`
   * Une propriété `data`, qui est un objet dans lequel les champs sont des enrichissements correspondant aux « noms » dans `output` et dont la valeur est considérée comme l’enrichissement.
   * Une propriété `errors`, tableau listant toutes les erreurs rencontrées et qui est ajouté à l’historique d’exécution de l’indexeur. Cette propriété est obligatoire, mais peut avoir une valeur `null`.
   * Une propriété `warnings`, tableau listant tous les avertissements rencontrés et qui est ajouté à l’historique d’exécution de l’indexeur. Cette propriété est obligatoire, mais peut avoir une valeur `null`.
* Les objets dans le tableau `values` ne sont pas nécessairement dans le même ordre que les objets dans le tableau `values` envoyé dans la demande à l’API web. Toutefois, comme le `recordId` est utilisé pour la corrélation, tous les enregistrements dans la réponse contenant un `recordId` absent de la demande d’origine envoyée à l’API web sont ignorés.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>Cas d’erreur
En plus de la non-disponibilité de votre API web ou de l’envoi de codes d’état non réussis, les cas suivants sont considérés comme erronés :

* Si l’API web retourne un code d’état de réussite, mais que la réponse indique que ce n’est pas `application/json`, la réponse est considérée comme non valide et aucun enrichissement n’est effectué.
* S’il y a des enregistrements **non valides** (dont le `recordId` n’est pas dans la demande d’origine ou qui contient des valeurs dupliquées) dans le tableau `values` de réponse, aucun enrichissement n’est effectué pour **ces** enregistrements.

Quand l’API web n’est pas disponible ou retourne une erreur HTTP, une erreur conviviale avec tous les détails disponibles sur l’erreur HTTP est ajoutée à l’historique d’exécution de l’indexeur.

## <a name="see-also"></a>Voir aussi

+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Ajouter une compétence personnalisée à la recherche cognitive](cognitive-search-custom-skill-interface.md)
+ [Exemple : Création d’une compétence personnalisée pour la recherche cognitive](cognitive-search-create-custom-skill-example.md)