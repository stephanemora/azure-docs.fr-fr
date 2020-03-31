---
title: Réessayer d’exécuter des tâches après une erreur
description: Recherchez les erreurs et réessayez l’exécution.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919989"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Détection et gestion des erreurs du service Batch

Il est important de toujours rechercher les erreurs éventuelles quand vous travaillez avec une API REST du service. En effet, il n’est pas rare que des erreurs se produisent durant l’exécution de travaux par lots.

## <a name="common-errors"></a>Erreurs courantes 

- Erreurs réseau : il s’agit de requêtes qui n’ont jamais atteint Batch ou dont la réponse de Batch n’a pas atteint le client dans le temps imparti.
- Erreurs internes du serveur : il s’agit de la réponse HTTP du code d’état 5xx standard.
- La limitation de bande passante peut provoquer des erreurs telles que des réponses HTTP du code d’état 429 ou 503 avec l’en-tête Retry-after.
- Erreurs 4xx qui incluent des erreurs comme AlreadyExists et InvalidOperation. Cela signifie que la ressource n’est pas dans l’état correct pour la transition d’état.

Pour plus d’informations sur les différents types de codes d’erreur et des codes d’erreur spécifiques, voir [État et codes d’erreur dans Batch](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Quand faire une nouvelle tentative

Les API du service Batch vous envoient une notification en cas d’erreur. Elles permettent toutes de faire une nouvelle tentative et elles incluent toutes un gestionnaire de nouvelles tentatives global à cet effet. Il est préférable d’utiliser ce mécanisme intégré.

Après une erreur, vous devez attendre un peu (quelques secondes entre deux nouvelles tentatives) avant de réessayer. Si vous réessayez trop souvent ou trop rapidement, le gestionnaire de nouvelles tentatives limite la bande passante.

### <a name="for-more-information"></a>Informations supplémentaires  

La rubrique [Outils et API Batch](batch-apis-tools.md) renvoie à des informations de référence sur les API. L’API .NET, par exemple, a une [classe RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) dans laquelle la stratégie de nouvelle tentative requise doit être spécifiée. 

Pour plus d’informations sur chaque API et leurs stratégies de nouvelles tentatives par défaut, consultez [État et codes d’erreur dans Batch](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
