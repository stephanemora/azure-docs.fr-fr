---
title: Contrôle d’accès concurrentiel | Microsoft Docs
description: Stratégies de contrôle d’accès concurrentiel pour les API de publication du Portail Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ecf0bb6ac7fc77e804c9fc8d62aba52810de5640
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625004"
---
<a name="concurrency-control"></a>Contrôle d’accès concurrentiel
===================

Chaque appel d’API de publication du Portail Cloud Partner doit spécifier explicitement la stratégie de contrôle d’accès concurrentiel à utiliser. L’absence de l’en-tête **If-Match** entraîne une réponse d’erreur HTTP 400. Nous proposons deux stratégies de contrôle d’accès concurrentiel.

-   **Optimiste** : le client appliquant la mise à jour vérifie si les données ont changé depuis la dernière lecture des données.
-   **Last one wins** (Dernier) : le client met à jour directement les données, indépendamment du fait qu’une autre application les a modifiées depuis la dernière lecture.

<a name="optimistic-concurrency-workflow"></a>Flux de travail de l’accès concurrentiel optimiste
-------------------------------

Nous vous recommandons d’utiliser la stratégie d’accès concurrentiel optimiste, avec le flux de travail suivant, afin de garantir qu’aucune modification inattendue n’est apportée à vos ressources.

1.  Récupérez une entité à l’aide des API. La réponse inclut une valeur ETag qui identifie la version actuellement stockée de l’entité (au moment de la réponse).
2.  Au moment de la mise à jour, incluez cette même valeur ETag dans l’en-tête de requête **If-Match** obligatoire.
3.  L’API compare la valeur ETag reçue dans la requête à la valeur ETag actuelle de l’entité dans une transaction atomique.
    *   Si les valeurs ETag sont différentes, l’API retourne une réponse HTTP `412 Precondition Failed`. Cette erreur indique qu’un autre processus a mis à jour l’entité depuis la dernière récupération par le client ou que la valeur ETag spécifiée dans la requête est incorrecte.
    *  Si les valeurs ETag sont identiques, ou si l’en-tête **If-Match** contient le caractère générique astérisque (`*`), l’API effectue l’opération demandée. L’opération de l’API met également à jour la valeur ETag stockée de l’entité.


> [!NOTE]
> La spécification du caractère générique (*) dans l’en-tête **If-Match** amène l’API à utiliser la stratégie d’accès concurrentiel Last-one-wins (Dernier). Dans ce cas, les valeurs ETag ne sont pas comparées et la ressource est mise à jour sans vérification. 
