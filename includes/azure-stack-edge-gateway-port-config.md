---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89081452"
---
| N° de port| Entrant ou sortant | Étendue de ports| Obligatoire|   Notes |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Sortie|WAN |Non|Le port de sortie est utilisé pour accéder à Internet afin de récupérer les mises à jour. <br>Le proxy web sortant est configurable par l’utilisateur. |
| TCP 443 (HTTPS)|Sortie|WAN|Oui|Le port de sortie est utilisé pour accéder aux données dans le cloud.<br>Le proxy web sortant est configurable par l’utilisateur.|
| UDP 123 (NTP)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur NTP Internet.  |   
| UDP 53 (DNS)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DNS Internet.<br>Nous vous recommandons d’utiliser un serveur DNS local. |
| TCP 5985 (WinRM)|Sortie/Entrée|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis pour se connecter à l’appareil via PowerShell à distance sur HTTP.  |
| UDP 67 (DHCP)|Sortie|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DHCP local.  |
| TCP 80 (HTTP)|Sortie/Entrée|LAN|Oui|Ce port est le port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. <br>L’accès à l’interface utilisateur locale par le biais du protocole HTTP est automatiquement redirigé vers HTTPS.  |
| TCP 443 (HTTPS)|Sortie/Entrée|LAN|Oui|Ce port est le port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. Ce port est également utilisé pour connecter Azure Resource Manager aux API locales de l’appareil, pour connecter le stockage Blob via des API REST et au service d’émission de jeton de sécurité (STS) pour l’authentification via des jetons d’accès et d’actualisation.|
| TCP 445 (SMB)|Dans|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous vous connectez via SMB. |
| TCP 2049 (NFS)|Dans|LAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous vous connectez via NFS. |


