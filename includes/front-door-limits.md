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
ms.openlocfilehash: 37ebe2f0c5cbbaca712e69ab4484379ecf0f4830
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237337"
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
| Stratégie de pare-feu d’applications web par abonnement | 100 |
| Conditions de correspondance du pare-feu d'applications web par règle personnalisée | 10 |
| Plages d’adresses IP du pare-feu d'applications web par condition de correspondance | 600 |
| Valeurs de correspondance de chaîne du pare-feu d'applications web par condition de correspondance | 10 |
| Longueur de valeur de correspondance de chaîne du pare-feu d'applications web | 256 |
| Longueur de nom de paramètre du corps POST du pare-feu d’applications web | 256 |
| Longueur du nom d’en-tête HTTP du pare-feu d’applications web | 256 |
| Longueur du nom de cookie du pare-feu d’applications web | 256 |
| Taille du corps de la requête HTTP inspectée du pare-feu d'applications web | 128 Ko |
| Longueur du corps de la réponse personnalisée du pare-feu d’applications web | 2 Ko |

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

### <a name="other-limits"></a>Autres limites
- Taille d’URL maximale : 8 192 octets - Spécifie la longueur maximale de l’URL brute (schéma + nom d’hôte + port + chemin + chaîne de requête de l’URL)
- Taille maximale de chaîne de requête : 4 096 octets - Spécifie la longueur maximale de la chaîne de requête en octets.
