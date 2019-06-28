---
title: Fichier Include
description: Fichier Include
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: e1f5a1c8229544d97d9ff64748390f0d5237ab97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238250"
---
| Ressource | Limite maximale/par défaut |
| --- | --- |
| Ressources Azure Front Door Service par abonnement | 100 |
| Hôtes front-end, y compris les domaines personnalisés par ressource | 100 |
| Règles de routage par ressource | 100 |
| Pools back-end par ressource | 50 |
| Serveurs principaux par pool back-end | 100 |
| Modèles de chemin à utiliser pour une règle de routage | 25 |
| Règles personnalisées de pare-feu d’applications web par stratégie | 10 |
| Stratégie de pare-feu d’applications web par ressource | 100 |

### <a name="timeout-values"></a>Valeurs de délai d’expiration
#### <a name="client-to-front-door"></a>Du client à Front Door
- Front Door a un délai de 61 secondes pour les connexions TCP inactives.

#### <a name="front-door-to-application-back-end"></a>De Front Door au serveur d’applications back-end
- Si la réponse est mémorisée en bloc, un code d’état de 200 est renvoyé lors de la réception du premier bloc.
- Une fois que la requête HTTP est transférée au serveur principal, Front Door attend 30 secondes l’arrivée du premier paquet depuis le serveur principal. Ensuite, il renvoie une erreur 503 au client.
- Lorsqu’il reçoit le premier paquet du serveur principal, Front Door attend 30 secondes (délai d’inactivité). Ensuite, il renvoie une erreur 503 au client.
- Le délai d’expiration d’une session TCP entre Front Door et un serveur principal est de 30 minutes.

### <a name="upload-and-download-data-limit"></a>Taille limite pour le chargement et le téléchargement des données

|  | Avec encodage de transfert mémorisé en bloc (CTE) | Sans segmentation HTTP |
| ---- | ------- | ------- |
| **Télécharger** | La taille du téléchargement n’est pas limitée. | La taille du téléchargement n’est pas limitée. |
| **Charger** |  Aucune limite tant que la taille de chaque chargement avec encodage de transfert mémorisé en bloc est inférieure à 2 Go. | Elle ne peut pas être supérieure à 2 Go. |
