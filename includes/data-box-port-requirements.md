---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839768"
---
| N° de port| Entrant ou sortant | Étendue de ports| Obligatoire| Notes |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Dans|LAN|Oui|Ce port est utilisé pour se connecter aux API REST de stockage Data Box Blog sur HTTP. Si vous ne vous connectez pas à des API REST, la connexion est automatiquement redirigée vers l’interface utilisateur web locale sur le port 8443. |
| TCP 443 (HTTPS)|Dans|LAN|Oui|Ce port est utilisé pour se connecter aux API REST de stockage Data Box Blog sur HTTPS. Si vous ne vous connectez pas à des API REST, la connexion est automatiquement redirigée vers l’interface utilisateur web locale sur le port 8443. |
| TCP 8443 (HTTPS-Alt)|Dans|LAN|Oui|Il s’agit d’un autre port pour le protocole HTTPS. il est utilisé lors de la connexion à l’interface utilisateur web locale pour la gestion des appareils. |
| TCP 445 (SMB)|Sortie/Entrée|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous vous connectez via SMB. |
| TCP 2049 (NFS)|Sortie/Entrée|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous vous connectez via NFS. |
| TCP 111 (NFS)|Sortie/Entrée|LAN|Dans certains cas<br>Voir les remarques|Ce port est utilisé pour le mappage rpcbind/port et requis uniquement si vous vous connectez via NFS.  |
