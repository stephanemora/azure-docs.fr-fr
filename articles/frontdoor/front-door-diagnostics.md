---
title: Azure Front Door Service - Métriques et journalisation | Microsoft Docs
description: Cet article présente les différents journaux d’accès et métriques pris en charge par Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997996"
---
# <a name="monitoring-metrics-for-front-door"></a>Métriques de supervision pour Front Door

Grâce à Azure Front Door Service, vous pouvez superviser vos métriques clés afin de valider la configuration Front Door, ainsi que l’utilisation, l’intégrité et les performances de votre porte d’entrée.

## <a name="metrics"></a>Métriques

Les métriques représentent une fonctionnalité de certaines ressources Azure, vous permettant d’afficher les compteurs de performances dans le portail. Pour Front Door, les métriques suivantes sont disponibles :

| Métrique | Nom d’affichage de la métrique | Unité | Dimensions | Description |
| --- | --- | --- | --- | --- |
| RequestCount | Nombre de requêtes | Count | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre de requêtes de clients prises en charge par Front Door  |
| RequestSize | Taille de la requête | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que requêtes de clients à Front Door. |
| ResponseSize | Taille de la réponse | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que réponses de Front Door aux clients. |
| TotalLatency | Latence totale | Millisecondes | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Temps calculé à partir du moment où la requête du client est reçue par Front Door jusqu’à ce que le client accuse réception du dernier octet de la réponse de Front Door. |
| BackendRequestCount | Nombre de requêtes de backend | Count | httpStatus</br>HttpStatusGroup</br>Backend | Nombre de requêtes envoyées de Front Door aux backends. |
| BackendRequestLatency | Latence de requête du backend | Millisecondes | Backend | Temps calculé à partir du moment où la requête est envoyée par Front Door au backend jusqu’à ce que Front Door reçoive le dernier octet de la réponse du backend. |
| BackendHealthPercentage | Pourcentage d’intégrité du backend | Pourcentage | Backend</br>BackendPool | Pourcentage de sondes d’intégrité réussies de Front Door vers les backends. |
| WebApplicationFirewallRequestCount | Nombre de requêtes du pare-feu d’applications web | Count | PolicyName</br>RuleName</br>Action | Nombre de requêtes de clients traitées par la sécurité de couche Application de Front Door. |


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
