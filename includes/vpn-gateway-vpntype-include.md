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
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159349"
---
* **Basé sur des stratégies :** Les VPN basés précédemment appelées passerelles de routage statique dans le modèle de déploiement classique. Les VPN basés sur des stratégies chiffrent et acheminent les paquets par le biais des tunnels IPsec basés sur les stratégies IPsec configurées avec les combinaisons de préfixes d’adresses entre votre réseau local et le réseau virtuel Azure. La stratégie (ou le sélecteur de trafic) est généralement définie en tant que liste d’accès dans les configurations du périphérique VPN. Un VPN de type basé sur des stratégies a pour valeur *PolicyBased*. Lorsque vous utilisez un VPN basé sur les itinéraires (PolicyBased), gardez à l’esprit les limitations suivantes :
  
  * Les VPN basés sur les itinéraires peuvent **uniquement** être utilisés sur la référence SKU de passerelle de base. Ce type de VPN n’est pas compatible avec les autres références SKU de passerelle.
  * Vous pouvez avoir 1 seul tunnel lors de l’utilisation d’un VPN basé sur les stratégies.
  * Vous pouvez uniquement utiliser les VPN basés sur les stratégies pour les connexions S2S et uniquement pour certaines configurations. La plupart des configurations de passerelle VPN requièrent un VPN basé sur les itinéraires.
* **RouteBased** : Basé sur un itinéraire VPN ont été précédemment appelées des passerelles de routage dynamique dans le modèle de déploiement classique. Les VPN basés sur un itinéraire utilisent des « itinéraires » dans l’adresse IP de transfert ou la table de routage pour acheminer des paquets dans leurs interfaces de tunnel correspondantes. Les interfaces de tunnel chiffrent ou déchiffrent ensuite les paquets se trouvant dans et hors des tunnels. La stratégie (ou le sélecteur de trafic) des VPN basés sur un itinéraire est configurée comme universelle (ou en caractères génériques). Le VPN de type basé sur un itinéraire a pour valeur *RouteBased*.
