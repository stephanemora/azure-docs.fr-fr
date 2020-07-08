---
title: Sélection du chemin Azure entre plusieurs liens ISP
titleSuffix: Azure Virtual WAN
description: Découvrir la sélection du chemin Azure et Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: cherylmc
ms.openlocfilehash: a83a050b65f5673a86dd07ec13842f6009d2c8dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753340"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Sélection du chemin Azure entre plusieurs liens ISP

Azure Virtual WAN permet à l’utilisateur d’inclure des informations de lien dans un site VPN, ce qui autorise les scénarios dans lesquels la solution d’appareil VPN/SD-WAN peut programmer des stratégies spécifiques à une branche pour diriger le trafic entre plusieurs liens vers Azure. Cela s’appelle **Sélection du chemin Azure**.

## <a name="architecture"></a>Architecture

Pour comprendre le fonctionnement de la sélection du chemin Azure, nous allons utiliser l’exemple d’un site VPN Virtual WAN et d’une connexion de site à site.

Un site VPN représente l’appareil SD-WAN/VPN local avec des informations telles que l’adresse IP publique, le modèle et le nom de l’appareil, etc. Le site VPN local réel peut avoir plusieurs liens ISP qui peuvent également être inclus dans les informations de site VPN Virtual WAN. Ainsi, vous pouvez voir les informations des liens dans Azure.

Une connexion IPsec de site à site entrant dans un VPN de Virtual WAN se termine sur les instances de passerelle VPN à l’intérieur d’un hub virtuel. Une connexion de site à site représente la connectivité entre le site VPN et la passerelle VPN Azure. Elle se compose d’une ou de plusieurs connexions de liaison. Chaque connexion de liaison est constituée de deux tunnels se terminant chacun sur une instance unique de la passerelle VPN Azure Virtual WAN. Il est possible de configurer jusqu’à quatre connexions de liaison dans la connexion de site à site, ce qui permet d’avoir jusqu’à huit tunnels au sein d’une connexion de site à site. Azure prend en charge jusqu’à 2 000 tunnels se terminant à l’intérieur d’une même passerelle VPN Virtual WAN.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagramme à liaisons multiples":::

Cette illustration montre plusieurs liaisons sur un site se connectant à Azure Virtual WAN. Dans ce diagramme :

* Il y a deux liens ISP sur la branche locale (appareil VPN/SD-WAN). Chaque lien ISP correspond à une connexion de liaison.

* Il est supposé que l’appareil VPN/SD-WAN local géré par le client prend en charge IPsec IKEv2 ou IKEv1.

* Chaque connexion Virtual WAN de site à site Azure renferme des connexions de liaison. Une connexion prend en charge jusqu’à quatre connexions de liaison. Azure facture des frais unitaires pour la connexion Virtual WAN. Les connexions de liaison ne sont pas facturées.

* Chaque connexion de liaison, à son tour, se compose de deux tunnels IPsec qui peuvent se terminer sur deux instances différentes de la passerelle VPN Virtual WAN. Les passerelles sont configurées en tant que passerelles en mode actif/actif pour la résilience. Chaque connexion de liaison doit avoir une adresse IP unique et une adresse IP de peering BGP. Dans le diagramme, le tunnel 0 peut se terminer sur l’instance 0, tandis que le tunnel 1 peut se terminer sur l’instance 1.

* Les appareils de branche qui fournissent la sélection du chemin peuvent activer la stratégie appropriée dans la solution de gestion de branche pour diriger le trafic entre plusieurs liens vers Azure. Par exemple, le lien ISP 1 peut être utilisé pour le trafic avec une priorité plus élevée, tandis que le lien ISP 2 peut être utilisé comme sauvegarde.

* Il est important de noter que le VPN du hub virtuel utilise le routage ECMP (Equal Cost Multi-path Routing) sur tous les tunnels de fin.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [FAQ Azure](virtual-wan-faq.md).