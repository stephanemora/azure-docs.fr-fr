---
title: API REST Azure App Configuration – Limitation
description: Pages de référence pour comprendre la limitation lors de l’utilisation de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 26464138b7739bb476d9d0dd1078194473ecd23e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423683"
---
# <a name="throttling"></a>Limitation

Les magasins de configuration sont limités par rapport aux demandes qu’ils peuvent traiter. Toutes les demandes dépassant un quota alloué pour un magasin de configuration recevront une réponse HTTP 429 (Trop de demandes).

La limitation est divisée en différentes stratégies de quota :

- **Nombre total de demandes** : nombre total de demandes
- **Bande passante totale** : données sortantes en octets
- **Stockage** : taille totale du stockage des données utilisateur en octets

## <a name="handling-throttled-responses"></a>Gestion des réponses limitées

Un fois la limite de débit pour un quota donné atteinte, le serveur répond aux demandes supplémentaires de ce type avec un code d’état _429_. La réponse _429_ contient un en-tête _retry-after-ms_ indiquant au client un délai d’attente suggéré (en millisecondes) pour permettre le réapprovisionnement du quota de demandes.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

Dans l’exemple ci-dessus, le client a dépassé son quota autorisé. Il est invité à ralentir et à attendre 10 millisecondes avant de tenter d’autres demandes. Les clients doivent également prendre en compte le backoff progressif.

## <a name="other-retry"></a>Autre nouvelle tentative

Le service peut identifier des situations autres que la limitation qui nécessitent une nouvelle tentative du client (par exemple, 503 Service indisponible). Dans tous ces cas, l’en-tête de réponse `retry-after-ms` est fourni. Pour renforcer la robustesse, le client est invité à respecter l’intervalle suggéré et à effectuer une nouvelle tentative.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
