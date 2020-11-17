---
title: API REST Azure App Configuration – Cohérence
description: Pages de référence pour garantir la cohérence en temps réel à l’aide de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423699"
---
# <a name="real-time-consistency"></a>Cohérence en temps réel

En raison de la nature de certains systèmes distribués, il est difficile d’appliquer la cohérence en temps réel entre les demandes de manière implicite. Une solution consiste à autoriser la prise en charge du protocole sous la forme de plusieurs **jetons de synchronisation**. Les jetons de synchronisation sont facultatifs.

## <a name="initial-request"></a>Demande initiale

Pour garantir la cohérence en temps réel entre les différentes instances et demandes de client, utilisez des en-têtes de demande/réponse `Sync-Token` facultatifs.

Syntaxe :

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Paramètre|Description|
|--|--|
| `<id>` | ID de jeton (opaque) |
| `<value>` | Valeur de jeton (opaque). Autorise une chaîne encodée en Base64. |
| `<sn>` | Numéro de séquence du jeton (version). La valeur la plus élevée correspond à une version plus récente du même jeton. Permet une meilleure concurrence et une mise en cache client. Le client peut choisir d’utiliser uniquement la dernière version du jeton, car les versions de jeton sont inclusives. Non requis pour les demandes. |

## <a name="response"></a>response

Le service fournit un en-tête `Sync-Token` avec chaque réponse.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Demandes suivantes

Il est garanti que toute demande ultérieure recevra une réponse cohérente en **temps réel** par rapport au `Sync-Token` fourni.

```http
Sync-Token: <id>=<value>
```

Si l’en-tête `Sync-Token` est omis de la demande, il est possible que le service réponde aux données mises en cache pendant une brève période de temps (jusqu’à quelques secondes) avant d’être réglé en interne. Ce comportement peut provoquer des lectures incohérentes si des modifications ont eu lieu immédiatement avant la lecture.

## <a name="multiple-sync-tokens"></a>Jetons de synchronisation multiples

Le serveur PEUT répondre avec plusieurs jetons de synchronisation à une même demande. Pour conserver la cohérence **en temps réel** pour la demande suivante, le client DOIT répondre avec tous les jetons de synchronisation reçus. Conformément au RFC, des valeurs d’en-tête multiples doivent être séparées par des virgules.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
