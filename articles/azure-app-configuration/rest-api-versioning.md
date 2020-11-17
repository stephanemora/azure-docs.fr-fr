---
title: API REST Azure App Configuration – Contrôle de version
description: Pages de référence pour le contrôle de version à l’aide de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423682"
---
# <a name="versioning"></a>Gestion de version

api-version : 1.0

Chaque demande du client doit fournir une version d’API explicite en tant que paramètre de chaîne de requête. Par exemple : `https://{myconfig}.azconfig.io/kv?api-version=1.0`

La valeur `api-version` est exprimée au format SemVer (majeure.mineure). La négociation de plage ou de version n’est pas prise en charge.

## <a name="error-response"></a>Réponse d’erreur

L’exemple suivant présente un résumé des réponses d’erreur possibles que le serveur retourne quand la version d’API n’a pas de correspondance.

### <a name="api-version-unspecified"></a>Version d’API non spécifiée

Se produit quand un client effectue une demande sans fournir de version d’API.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>Version d’API non prise en charge

Se produit quand une version d’API demandée par un client ne correspond à aucune des versions d’API que le serveur prend en charge.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>Version d’API non valide

Se produit quand un client effectue une demande avec une version d’API dont la valeur est mal formée ou ne peut pas être analysée par le serveur.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>Version d’API ambiguë

Se produit quand un client demande une version d’API ambiguë pour le serveur. Par exemple, en présence de plusieurs valeurs différentes.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
