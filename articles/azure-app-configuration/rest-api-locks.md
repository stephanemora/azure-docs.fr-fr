---
title: API REST Azure App Configuration – Verrous
description: Pages de référence pour l’utilisation de verrous de clé-valeur à l’aide de l’API REST Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7e63b48f2119c48cd43717acee7b13b1701e0032
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241265"
---
# <a name="locks"></a>Verrous

Cette API (version 1.0) offre une sémantique de verrouillage et de déverrouillage pour la ressource de clé-valeur. Elle prend en charge les opérations suivantes :

- Placer un verrou
- Retirer un verrou

Si le paramètre `label` est présent, il doit s’agir d’une valeur d’étiquette explicite (et non d’un caractère générique). Pour toutes les opérations, il est facultatif. Son omission implique l’absence d’étiquette.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Verrouillage de la clé-valeur

- Obligatoire : ``{key}``, ``{api-version}``  
- Facultatif : ``label``

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

## <a name="unlock-key-value"></a>Déverrouillage de la clé-valeur

- Obligatoire : ``{key}``, ``{api-version}``  
- Facultatif : ``label``

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

## <a name="conditional-lock-and-unlock"></a>Verrouillage et déverrouillage conditionnel

Pour éviter les conditions de concurrence, utilisez les en-têtes de demande `If-Match` ou `If-None-Match`. L’argument `etag` fait partie de la représentation clé. Si `If-Match` et `If-None-Match` sont omis, l’opération est inconditionnelle.

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
