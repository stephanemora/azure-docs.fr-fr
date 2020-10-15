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
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854101"
---
| Ressource                                | Limite        |
|-----------------------------------------|------------------------------|
| Préfixes d’adresse de réseau virtuel                   | 600 par passerelle VPN          |
| Agréger des itinéraires BGP                    | 4 000 par passerelle VPN        |
| Préfixes d’adresses de passerelle réseau locale  | 1 000 par passerelle réseau locale.               |
| Connexions S2S                         | [Dépend de la référence (SKU) de la passerelle](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Connexions P2S                         | [Dépend de la référence (SKU) de la passerelle](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| Limite d’itinéraire P2S – IKEv2                 | 256 pour non Windows **/** 25 pour Windows           |
| Limite d’itinéraire P2S – OpenVPN               | 1 000                         |
| Bande passante flows                              | 100 000 pour VpnGw1/AZ **/** 512 Ko pour VpnGw2-4/AZ|