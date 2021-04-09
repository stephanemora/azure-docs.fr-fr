---
title: Fichier include
description: Fichier include
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559637"
---
| Ressource                                | Limite        |
|-----------------------------------------|------------------------------|
| Préfixes d’adresse de réseau virtuel                   | 600 par passerelle VPN          |
| Agréger des itinéraires BGP                    | 4 000 par passerelle VPN        |
| Préfixes d’adresses de passerelle réseau locale  | 1 000 par passerelle réseau locale.               |
| Connexions S2S                         | [Dépend de la référence (SKU) de la passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| Connexions P2S                         | [Dépend de la référence (SKU) de la passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| Limite d’itinéraire P2S – IKEv2                 | 256 pour non Windows **/** 25 pour Windows           |
| Limite d’itinéraire P2S – OpenVPN               | 1 000                         |
| Bande passante flows                              | 100 000 pour VpnGw1/AZ **/** 512 Ko pour VpnGw2-4/AZ|