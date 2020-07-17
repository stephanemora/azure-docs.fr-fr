---
title: 'Scénario : Router le trafic via une NVA'
titleSuffix: Azure Virtual WAN
description: Router le trafic via une NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142964"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scénario : Router le trafic via une NVA

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans ce scénario d’appliance virtuelle réseau, l’objectif est d’acheminer le trafic via une NVA (appliance virtuelle réseau) pour la branche vers le VNet et pour le VNet vers la branche. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

> [!NOTE]
> Certaines fonctionnalités de routage peuvent toujours être en cours de déploiement. Si le déploiement n’a pas encore eu lieu dans votre région, veuillez suivre entre-temps les étapes de ces versions des articles :
>* [Article concernant le portail Azure](virtual-wan-route-table-nva-portal.md)
>* [Article concernant PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>Architecture du scénario

Dans **Figure 1**, il y a deux hubs : **Hub 1** et **Hub 2**.

* **Hub 1** et **Hub 2** sont connectés directement aux réseaux virtuels de NVA **VNET 2** et **VNET 4**.

* **VNET 5** et **VNET 6** sont appairés avec **VNET 2**.

* **VNET 7** et **VNET 8** sont appairés avec **VNET 4**.

* **Les VNET 5, 6, 7 et 8** sont des réseaux en étoile, qui ne sont pas directement connectés à un hub virtuel.

**Figure 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Figure 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Workflow du scénario

Pour configurer le routage via la NVA, voici les étapes à prendre en compte :

1. Identifiez la connexion du réseau virtuel en étoile à la NVA. Dans la  **Figure 1**, ils y a une **connexion VNET 2 (eastusconn)** et une **connexion VNET 4 (weconn)** .

   Vérifiez que des itinéraires définis par l’utilisateur (UDR) sont configurés :
   * De VNET 5 et 6 à l’IP NVA de VNET 2
   * De VNET 7 et 8 à l’IP NVA de VNET 4 
   
   Vous n’avez pas à connecter VNET 5,6,7,8 directement aux hubs virtuels. Assurez-vous que les NSG dans les VNET 5,6,7,8 autorisent le trafic pour la branche (VPN/ER/P2S) ou aux VNET connectés à leurs VNET distants. Par exemple, les VNET 5,6 doivent garantir que les NSG autorisent le trafic pour les préfixes d’adresses locaux et les VNET 7,8 qui sont connectés au hub distant 2. 

2. Ajoutez une entrée d’itinéraire statique agrégée pour les VNET 2,5,6 à la table de routage par défaut du Hub 1. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Exemple":::

3. Configurez un itinéraire statique pour les VNET 5, 6 dans la connexion de réseau virtuel du VNET 2. Pour configurer la configuration du routage pour une connexion de réseau virtuel, consultez [routage de hub virtuel](how-to-virtual-hub-routing.md#routing-configuration).

4. Ajoutez une entrée d’itinéraire statique agrégée pour les VNET 4,7,8 à la table de route par défaut du Hub 1.

5. Répétez les étapes 2, 3 et 4 pour la table de route par défaut du Hub 2.

Cela entraînera des modifications de configuration de routage, comme illustré dans la figure ci-dessous

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Résultat":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
