---
title: Fichier Include
description: Fichier Include
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: f0c2d1501b9aa19dec8c4ad157e004a57e0e5070
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006485"
---
| Ressource | Limite par défaut |
| --- | --- |
| Ressources Front Door par abonnement | 100 |
| Hôtes frontend, y compris les domaines personnalisés par ressource | 100 |
| Règles de routage par ressource | 100 |
| Pools backend par ressource | 50 |
| Serveurs backend par pool backend | 100 |
| Modèles de chemin à utiliser pour une règle de routage | 25 |
| Règles personnalisées de pare-feu d’applications web par stratégie | 10 |
| Stratégie de pare-feu d’applications web par ressource | 100 |

### <a name="timeout-values"></a>Valeurs de délai d’expiration
#### <a name="client-to-front-door"></a>Du client à Front Door
- Front Door a un délai de 61 secondes pour les connexions TCP inactives.
#### <a name="front-door-to-application-backend"></a>De Front Door au serveur d’applications backend
- Si la réponse est une réponse mémorisée en bloc, un code d’état 200 est retourné quand le premier bloc est reçu.
- Une fois la requête HTTP transférée vers le serveur backend, Front Door attend 30 secondes que le premier paquet provenant du serveur backend arrive, avant de retourner une erreur 503 au client.
- Lorsque le premier paquet du serveur backend est reçu, Front Door attend 30 secondes (délai d’inactivité) avant de retourner une erreur 503 au client.
- Le délai d’expiration d’une session TCP entre Front Door et un serveur backend est de 30 minutes.

### <a name="upload--download-data-limit"></a>Limite de chargement et de téléchargement des données

|  | Avec encodage de transfert mémorisé en bloc | Sans segmentation HTTP |
| ---- | ------- | ------- |
| **Télécharger** | La taille du téléchargement n’est pas limitée | La taille du téléchargement n’est pas limitée |
| **Charger** |  Aucune limite tant que chaque chargement avec encodage de transfert mémorisé en bloc est inférieur à 28,6 Mo | La taille du chargement ne peut pas être supérieure à 28,6 Mo. |
