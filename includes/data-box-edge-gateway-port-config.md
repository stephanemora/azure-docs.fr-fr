---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 67de9042af11a2b17c4b65f8225ecd0580b95466
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263474"
---
| N° de port| Entrant ou sortant | Étendue de ports| Obligatoire|   Notes |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Sortie|WAN |Non |Le port de sortie est utilisé pour accéder à Internet afin de récupérer les mises à jour. <br>Le proxy web sortant est configurable par l’utilisateur. |
| TCP 443 (HTTPS)|Sortie|WAN|Oui|Le port de sortie est utilisé pour accéder aux données dans le cloud.<br>Le proxy web sortant est configurable par l’utilisateur.|   
| UDP 53 (DNS)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DNS Internet.<br>Nous vous recommandons d’utiliser un serveur DNS local. |
| UDP 123 (NTP)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur NTP Internet.  |
| UDP 67 (DHCP)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DHCP.  |
| TCP 80 (HTTP)|Dans|LAN|Oui|Ce port est le port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. <br>L’accès à l’interface utilisateur locale par le biais du protocole HTTP est automatiquement redirigé vers HTTPS.  |
| TCP 443 (HTTPS)|Dans|LAN|Oui|Ce port est le port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. |