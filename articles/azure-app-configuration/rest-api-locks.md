---
title: API REST Azure App Configuration – Verrous
description: Pages de référence pour l’utilisation de verrous de clé-valeur à l’aide de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4949db646c54d75f60d29d3c631d0f4ee8d7c26e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423692"
---
# <a name="locks"></a>Verrous

api-version : 1.0

Cette API fournit une sémantique de verrouillage/déverrouillage pour la ressource de clé-valeur. Elle prend en charge les opérations suivantes :

- Placer un verrou
- Retirer un verrou

Si la valeur `label` est présente, il doit s’agir d’une valeur d’étiquette explicite (**pas** d’un caractère générique). Pour toutes les opérations, ce paramètre est facultatif. Son omission implique l’absence d’étiquette.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Verrouiller la clé-valeur

- **Obligatoire :** ``{key}``, ``{api-version}``  
- *Facultatif :* ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Réponses :**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Si la clé-valeur n’existe pas, la réponse suivante est retournée :

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Déverrouiller la valeur de clé

- **Obligatoire :** ``{key}``, ``{api-version}``  
- *Facultatif :* ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Réponses :**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Si la clé-valeur n’existe pas, la réponse suivante est retournée :

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lockunlock"></a>Verrouillage/déverrouillage conditionnel

Pour éviter les conditions de concurrence, utilisez les en-têtes de demande `If-Match` ou `If-None-Match`. L’argument `etag` fait partie de la représentation de clé. En cas d’omission de l’en-tête `If-Match` ou `If-None-Match`, l’opération est inconditionnelle.

La requête suivante applique l’opération uniquement si la représentation de la clé-valeur actuelle correspond à l’`etag` spécifié :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

La requête suivante applique l’opération uniquement si la représentation de la clé-valeur actuelle existe, mais ne correspond pas à l’`etag` spécifié :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
