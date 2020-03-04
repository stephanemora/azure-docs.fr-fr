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
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651931"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Détection et gestion des erreurs du service Batch

Il est important de toujours rechercher les erreurs éventuelles quand vous travaillez avec une API REST du service. En effet, il n’est pas rare que des erreurs se produisent durant l’exécution de travaux par lots.

## <a name="common-errors"></a>Erreurs courantes 

- Erreurs réseau : il s’agit de requêtes qui n’ont jamais atteint Batch ou dont la réponse de Batch n’a pas atteint le client dans le temps imparti.
- Erreurs internes du serveur : il s’agit de la réponse HTTP du code d’état 5xx standard.
- La limitation de bande passante peut provoquer des erreurs telles que des réponses HTTP du code d’état 429 ou 503 avec l’en-tête Retry-after.
- Erreurs 4xx qui incluent des erreurs comme AlreadyExists et InvalidOperation. Cela signifie que la ressource n’est pas dans l’état correct pour la transition d’état.

## <a name="when-to-retry"></a>Quand faire une nouvelle tentative

Les API du service Batch vous envoient une notification en cas d’erreur. Elles permettent toutes de faire une nouvelle tentative et elles incluent toutes un gestionnaire de nouvelles tentatives global à cet effet. Il est préférable d’utiliser ce mécanisme intégré.

Après une erreur, vous devez attendre un peu (quelques secondes entre deux nouvelles tentatives) avant de réessayer. Si vous réessayez trop souvent ou trop rapidement, le gestionnaire de nouvelles tentatives limite la bande passante.


Pour plus d’informations sur chaque API et leurs stratégies de nouvelles tentatives par défaut, consultez [État et codes d’erreur dans Batch](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
