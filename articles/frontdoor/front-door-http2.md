---
title: Azure Front Door - Prise en charge du protocole HTTP2 | Microsoft Docs
description: Cet article vous aide à en savoir plus sur la prise en charge du protocole HTTP/2 dans Azure Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 8a3ae8065553b34a72528cb0f2681e327dc90097
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985182"
---
# <a name="http2-support-in-azure-front-door"></a>Prise en charge du protocole HTTP/2 dans Azure Front Door

Actuellement, la prise en charge de HTTP/2 est active pour toutes les configurations Azure Front Door. Aucune action supplémentaire n’est nécessaire de la part des clients.

HTTP/2 est une révision majeure du protocole HTTP/1.1. Il offre des performances web plus rapides, des meilleurs temps de réponse et une expérience utilisateur améliorée, tout en conservant les méthodes HTTP, les codes d’état et la sémantique habituels. Bien que HTTP/2 soit conçu pour fonctionner avec HTTP et HTTPS, de nombreux navigateurs web clients prennent en charge seulement HTTP/2 sur TLS (Transport Layer Security).

> [!NOTE]
> La prise en charge du protocole HTTP/2 est disponible uniquement pour les requêtes des clients à Front Door. La communication entre Front Door et les serveurs principaux dans le pool back-end s’effectue via HTTP/1.1. 

### <a name="http2-benefits"></a>Avantages de HTTP/2

Les avantages de HTTP/2 sont les suivants :

*   **Multiplexage et accès concurrentiel**

    Avec HTTP 1.1, effectuer plusieurs demandes de ressources nécessite plusieurs connexions TCP, et chaque connexion représente une surcharge qui a un impact sur les performances. HTTP/2 permet de demander plusieurs ressources sur une même connexion TCP.

*   **Compression des en-têtes**

    Grâce à la compression des en-têtes HTTP pour les ressources traitées, le temps passé sur le réseau est sensiblement réduit.

*   **Dépendances de flux**

    Les dépendances de flux permettent au client indiquer au serveur quelles ressources ont la priorité.


## <a name="http2-browser-support"></a>Prise en charge de HTTP/2 par les navigateurs

Tous les principaux navigateurs ont implémenté la prise en charge de HTTP/2 dans leur version actuelle. Les navigateurs non pris en charge passent automatiquement à HTTP/1.1.

|Browser|Version minimale|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les HTTP/2, consultez les ressources suivantes :

- [Page d’accueil de la spécification HTTP/2](https://http2.github.io/)
- [Forum Aux Questions officiel de HTTP/2](https://http2.github.io/faq/)
- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
