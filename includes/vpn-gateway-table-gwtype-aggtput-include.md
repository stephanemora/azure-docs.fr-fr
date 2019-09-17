---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919673"
---
|**Référence (SKU)**   | **S2S/VNet-to-VNet<br>Tunnels** | **P2S<br> connexions SSTP** | **P2S<br> connexions IKEv2/OpenVPN** | **Agrégat<br>Référence de débit** | **BGP** | **Redondant interzone** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**De base** | Bande passante 10    | Bande passante 128  | Non pris en charge  | 100 Mbits/s  | Non pris en charge| Non |
|**VpnGw1**| Bande passante 30*   | Bande passante 128  | Bande passante 250       | 650 Mbits/s  | Pris en charge | Non |
|**VpnGw2**| Bande passante 30*   | Bande passante 128  | Bande passante 500       | 1 Gbit/s    | Pris en charge | Non |
|**VpnGw3**| Bande passante 30*   | Bande passante 128  | Bande passante 1 000      | 1,25 Gbits/s | Pris en charge | Non |
|**VpnGw1AZ**| Bande passante 30*   | Bande passante 128  | Bande passante 250       | 650 Mbits/s  | Pris en charge | OUI |
|**VpnGw2AZ**| Bande passante 30*   | Bande passante 128  | Bande passante 500       | 1 Gbit/s    | Pris en charge | OUI |
|**VpnGw3AZ**| Bande passante 30*   | Bande passante 128  | Bande passante 1 000      | 1,25 Gbits/s | Pris en charge | OUI |

(*) Utilisez le [WAN virtuel](../articles/virtual-wan/virtual-wan-about.md) si vous avez besoin de plus de 30 tunnels VPN S2S.

* Ces limites de connexion sont séparées. Par exemple, vous pouvez avoir 128 connexions SSTP et 250 connexions IKEv2 sur une référence SKU VpnGw1.

* Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Vous trouverez des informations relatives au contrat de niveau de service (SLA) sur la page [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2, VpnGw3, VpnGw1AZ, VpnGw2AZ et VpnGw3AZ sont prises en charge seulement pour les passerelles VPN qui utilisent le modèle de déploiement du Gestionnaire des ressources.

* La référence SKU de base est considérée comme une référence SKU héritée. La référence SKU De base a certaines limitations en termes de fonctionnalités. Vous ne pouvez pas redimensionner une passerelle qui utilise une référence SKU De base vers l’une des nouvelles références SKU de passerelle. À la place, vous devez passer à une nouvelle référence SKU, ce qui implique la suppression et la recréation de votre passerelle VPN. Vérifiez que la fonctionnalité dont vous avez besoin est prise en charge avant d’utiliser la référence SKU De base.

* La référence de débit agrégée est basée sur les mesures de plusieurs tunnels agrégés via une passerelle unique. Le test d’évaluation du débit d’agrégat pour une passerelle VPN est S2S + P2S combinés. **Si vous avez un grand nombre de connexions P2S, cela peut avoir un impact négatif sur la connexion S2S à cause des limites de débit.** La référence de débit agrégée n’est pas garantie en raison des conditions de trafic Internet et des comportements de votre application.

* Pour aider nos clients à comprendre les performances relatives des références SKU VpnGw, nous avons utilisé les outils disponibles publiquement iPerf et CTSTraffic pour mesurer les performances. Le tableau ci-dessous liste les résultats des tests de performances avec différents algorithmes. Comme vous pouvez le voir, les meilleures performances sont obtenues quand nous utilisons l’algorithme GCMAES256 pour le chiffrement IPsec et pour l’intégrité. Nous obtenons des performances moyennes lors de l’utilisation d’AES256 pour le chiffrement IPsec et de SHA256 pour l’intégrité. Quand nous utilisons DES3 pour le chiffrement IPsec et SHA256 pour l’intégrité, nous obtenons les performances les plus faibles.

|**Référence (SKU)**   | **Algorithmes<br>utilisés** | **Débit<br>observé** | **Paquets par seconde<br>observés** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 et SHA256<br>DES3 et SHA256| 650 Mbits/s<br>500 Mbits/s<br>120 Mbits/s   | 58 000<br>50 000<br>50 000|
|**VpnGw2**| GCMAES256<br>AES256 et SHA256<br>DES3 et SHA256| 1 Gbit/s<br>500 Mbits/s<br>120 Mbits/s | 90 000<br>80 000<br>55 000|
|**VpnGw3**| GCMAES256<br>AES256 et SHA256<br>DES3 et SHA256| 1,25 Gbits/s<br>550 Mbits/s<br>120 Mbits/s | 105 000<br>90 000<br>60 000|
|**VpnGw1AZ**| GCMAES256<br>AES256 et SHA256<br>DES3 et SHA256| 650 Mbits/s<br>500 Mbits/s<br>120 Mbits/s   | 58 000<br>50 000<br>50 000|
|**VpnGw2AZ**| GCMAES256<br>AES256 et SHA256<br>DES3 et SHA256| 1 Gbit/s<br>500 Mbits/s<br>120 Mbits/s | 90 000<br>80 000<br>55 000|
|**VpnGw3AZ**| GCMAES256<br>AES256 et SHA256<br>DES3 et SHA256| 1,25 Gbits/s<br>550 Mbits/s<br>120 Mbits/s | 105 000<br>90 000<br>60 000|
