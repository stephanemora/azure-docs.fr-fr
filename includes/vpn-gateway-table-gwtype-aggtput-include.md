---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b698dd03473dd3cb708c47c6554869eebba48bf9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597603"
---
|**Référence (SKU)**   | **S2S/VNet-to-VNet<br>Tunnels** | **P2S<br> connexions SSTP** | **P2S<br> connexions IKEv2** | **Agrégat<br>Référence de débit** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**De base** | Bande passante 10    | Bande passante 128  | Non pris en charge  | 100 Mbits/s  | Non pris en charge|
|**VpnGw1**| Bande passante 30*   | Bande passante 128  | Bande passante 250       | 650 Mbits/s  | Pris en charge |
|**VpnGw2**| Bande passante 30*   | Bande passante 128  | Bande passante 500       | 1 Gbit/s    | Pris en charge |
|**VpnGw3**| Bande passante 30*   | Bande passante 128  | Bande passante 1 000      | 1,25 Gbits/s | Pris en charge |


(*) Utilisez le [WAN virtuel](../articles/virtual-wan/virtual-wan-about.md) si vous avez besoin de plus de 30 tunnels VPN S2S.

* Le test d’évaluation du débit d’agrégat pour une passerelle VPN est S2S + P2S combinés. **Si vous avez un grand nombre de connexions P2S, cela peut avoir impact négatif sur la connexion S2S à cause des limites de débit.** La référence de débit agrégée est basée sur les mesures de plusieurs tunnels agrégés via une passerelle unique. Le débit n’est pas garanti en raison de conditions de trafic Internet et des comportements de votre application.

* Ces limites de connexion sont séparées. Par exemple, vous pouvez avoir 128 connexions SSTP et 250 connexions IKEv2 sur une référence SKU VpnGw1.

* Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Vous trouverez des informations relatives au contrat de niveau de service (SLA) sur la page [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2 et VpnGw3 sont compatibles avec les passerelles VPN qui utilisent le modèle de déploiement du Gestionnaire des ressources uniquement.
