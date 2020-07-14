---
title: Contrôle d’accès concurrentiel – Place de marché Azure
description: Stratégies de contrôle d’accès concurrentiel pour les API de publication du Portail Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: b66d266500745d08bef98a42e51cc8a7bab63958
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102733"
---
# <a name="concurrency-control"></a>Contrôle d’accès concurrentiel

> [!NOTE]
> Les API du portail Cloud Partner sont intégrées à l’Espace partenaires et continueront à fonctionner après la migration de vos offres vers l’Espace partenaires. L’intégration apporte de légères modifications. Passez en revue les changements listés dans la [référence API du portail Cloud Partner](./cloud-partner-portal-api-overview.md) pour vous assurer que votre code continue à fonctionner après la migration vers l’Espace partenaires.

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
