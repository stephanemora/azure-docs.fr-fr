---
title: API REST Azure App Configuration – Étiquettes
description: Pages de référence pour l’utilisation d’étiquettes à l’aide de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e6fcc8399c1dbc36a5b2e915c726f1c2496ee2f9
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423645"
---
# <a name="labels"></a>Étiquettes

api-version : 1.0

La ressource **Étiquette** est définie comme suit :

```json
{
      "name": [string]             // Name of the label
}
```

Prend en charge les opérations suivantes :

- List

Pour toutes les opérations, ``name`` est un paramètre de filtre facultatif. Son omission implique **toute** étiquette.

## <a name="prerequisites"></a>Prérequis

- Toutes les requêtes HTTP doivent être authentifiées. Consultez la section [Authentification](./rest-api-authentication-index.md).
- Toutes les requêtes HTTP doivent fournir des `api-version` explicites. Consultez la section [Contrôle de version](./rest-api-versioning.md).

## <a name="list-labels"></a>Répertorier les étiquettes

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Réponses :**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Pagination

Le résultat est paginé si le nombre d’éléments retournés dépasse le nombre limite de réponses. Suivez les en-têtes de réponse `Link` facultatifs et utilisez `rel="next"` pour la navigation. Le contenu fournit également un lien suivant sous forme de la propriété `@nextLink`. Le lien suivant contient le paramètre `api-version`.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Réponse :**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Filtrage

Le filtrage par `name` est pris en charge.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtres pris en charge

|Filtre de clé|Résultat|
|--|--|
|`name` est omis ou `name=*`|Correspond à **toute** étiquette|
|`name=abc`|Correspond à une étiquette nommée **abc**|
|`name=abc*`|Correspond aux noms d’étiquette qui commencent par **abc**|
|`name=abc,xyz`|Correspond aux noms d’étiquette **abc** ou **xyz** (limités à 5 valeurs séparées par des virgules)|

### <a name="reserved-characters"></a>Caractères réservés

`*`, `\`, `,`

Si un caractère réservé fait partie de la valeur, il doit être placé dans une séquence d’échappement à l’aide de `\{Reserved Character}`. Les caractères non réservés peuvent également être placés dans une séquence d’échappement.

### <a name="filter-validation"></a>Validation de filtre

Si une erreur de validation de filtre se produit, la réponse est HTTP `400` avec les détails de l’erreur :

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Exemples

- Tous

    ```http
    GET /labels?api-version={api-version}
    ```

- Le nom d’étiquette commence par **abc**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- Le nom d’étiquette est **abc** ou **xyz**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Champs spécifiques de la demande

Utilisez le paramètre de chaîne de requête facultatif `$select` et fournissez une liste séparée par des virgules des champs demandés. Si le paramètre `$select` est omis, la réponse contient l’ensemble par défaut.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Accès basé sur le temps

Obtenez une représentation du résultat tel qu’il était à un moment passé. Voir la section [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Réponse :**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
