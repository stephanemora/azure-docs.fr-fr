---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39138091"
---
|**Référence (SKU)**   | **S2S/VNet-to-VNet<br>Tunnels** | **P2S<br>Connexions** | **Agrégat<br>Référence de débit** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Bande passante 30*                         | Bande passante 128**             | 650 Mbits/s                    |
|**VpnGw2**| Bande passante 30*                         | Bande passante 128**             | 1 Gbit/s                      |
|**VpnGw3**| Bande passante 30*                         | Bande passante 128**             | 1,25 Gbits/s                   |
|**De base** | Bande passante 10                         | Bande passante 128               | 100 Mbits/s                    | 

* (*) Utilisez le [WAN virtuel](../articles/virtual-wan/virtual-wan-about.md) si vous avez besoin de plus de 30 tunnels VPN S2S.

* (**) Contactez le support technique si des connexions supplémentaires sont nécessaires

* La référence de débit agrégée est basée sur les mesures de plusieurs tunnels agrégés via une passerelle unique. Le débit n’est pas garanti en raison de conditions de trafic Internet et des comportements de votre application.

* Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Vous trouverez des informations relatives au contrat de niveau de service (SLA) sur la page [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2 et VpnGw3 sont compatibles avec les passerelles VPN qui utilisent le modèle de déploiement du Gestionnaire des ressources uniquement.