---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0377548a3b026657ae3282801523b1c0c6731265
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236688"
---
À compter du 1er juillet 2018, la passerelle VPN Azure ne prendra plus en charge TLS 1.0 et 1.1. Elle prendra uniquement en charge TLS 1.2. Seules les connexions de point à site sont affectées ; les connexions site à site ne le sont pas. Si vous utilisez le protocole TLS pour les VPN de point à site sur des clients Windows 10, aucune action n’est nécessaire. Si vous utilisez le protocole TLS pour les connexions de point à site sur des clients Windows 7 et Windows 8, consultez [Questions fréquentes (FAQ) sur la passerelle VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) pour obtenir des instructions de mise à jour.