---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2019
ms.author: alkohli
ms.openlocfilehash: 783ae29e9ca0c9a609d1d1d283525a952c2f7f33
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118688"
---
| N° de port| Entrant ou sortant | Étendue de ports| Obligatoire|   Notes |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Sortie/Entrée|WAN  |Non |Le port de sortie est utilisé pour accéder à Internet afin de récupérer les mises à jour. <br>Le proxy web sortant est configurable par l’utilisateur. |
| TCP 443 (HTTPS)|Sortie/Entrée|WAN|OUI|Le port de sortie est utilisé pour accéder aux données dans le cloud.<br>Le proxy web sortant est configurable par l’utilisateur.|
| UDP 123 (NTP)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur NTP Internet.  |   
| UDP 53 (DNS)|Sortie|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DNS Internet.<br>Nous vous recommandons d’utiliser un serveur DNS local. |
| TCP 5985 (WinRM)|Sortie/Entrée|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis pour se connecter à l’appareil via PowerShell à distance sur HTTP.  |
| UDP 67 (DHCP)|Sortie|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DHCP local.  |
| TCP 80 (HTTP)|Sortie/Entrée|LAN|OUI|Ce port est le port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. <br>L’accès à l’interface utilisateur locale par le biais du protocole HTTP est automatiquement redirigé vers HTTPS.  |
| TCP 443 (HTTPS)|Sortie/Entrée|LAN|OUI|Ce port est le port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. |
| TCP 445 (SMB)|Dans|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous vous connectez via SMB. |
| TCP 2049 (NFS)|Dans|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous vous connectez via NFS. |

