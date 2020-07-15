---
title: 'Scénario : Acheminer le trafic via des appliances virtuelles réseau à l’aide de paramètres personnalisés'
titleSuffix: Azure Virtual WAN
description: Ce scénario vous aide à acheminer le trafic via des appliances virtuelles réseau en utilisant une appliance virtuelle réseau différente pour le trafic lié à Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567907"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scénario : Acheminer le trafic via des appliances virtuelles réseau – Personnalisé (préversion)

Lorsque vous utilisez le routage de hub virtuel Virtual WAN, un certain nombre de scénarios sont possibles. Dans ce scénario d’appliance virtuelle réseau, l’objectif est d’acheminer le trafic via une appliance virtuelle réseau pour la liaison entre le réseau virtuel et la branche, et d’utiliser une appliance virtuelle réseau distincte pour le trafic Internet. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architecture du scénario

La **Figure 1** présente un hub, **Hub 1**.

* Le **Hub 1** est connecté directement aux réseaux virtuels d’appliance virtuelle réseau (NVA) **VNET 5** et **VNET 4**.

* Le trafic entre les réseaux virtuels 1, 2 et 3 et les branches (VPN/ER/P2S) doit supposé transiter via **VNET 4 NVA** 10.4.0.5.

* Tout le trafic Internet en provenance des réseaux virtuels 1, 2 et 3 est supposé transiter via **VNET 5 NVA** 10.5.0.5.

**Figure 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figure 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Workflow du scénario

Pour configurer le routage via la NVA, voici les étapes à suivre :

1. Pour que le trafic Internet transite via VNET5, vous avez besoin des réseaux virtuels 1, 2 et 3 pour vous connecter directement à VNET 5 via VNet Peering. Vous avez également besoin d’un itinéraire défini par l’utilisateur configuré dans le réseaux virtuels pour 0.0.0.0/0 et le tronçon suivant 10.5.0.5. Actuellement, Virtual WAN n’autorise pas une appliance virtuelle réseau de tronçon suivant dans le hub virtuel pour 0.0.0.0/0.

1. Dans le portail Azure, accédez à votre hub virtuel et créez une table de routage personnalisée **RT_Shared** qui apprendra les itinéraires via la propagation de toutes les connexions de réseaux virtuels et de branche. La **Figure 2** illustre cela sous la forme d’une table de routage personnalisé vide **RT_Shared**.

   * **Itinéraires :** vous n’avez pas besoin d’ajouter des itinéraires statiques.

   * **Association :** sélectionnez les réseaux virtuels 4 et 5, ce qui signifie que leurs connexions s’associent à la table de routage **RT_Shared**.

   * **Propagation :** étant donné que vous souhaitez que l’ensemble des branches et des connexions de réseau virtuel propagent leurs itinéraires de manière dynamique vers cette table de routage, sélectionnez les branches et tous les réseaux virtuels.

1. Créez une table de routage personnalisée **RT_V2B** pour acheminer le trafic des réseaux virtuels 1, 2 et 3 vers les branches.

   * **Itinéraires :** Ajoutez une entrée d’itinéraire statique agrégée pour les branches (VPN/ER/P2S) (10.2.0.0/16 dans la **Figure 2**) avec un tronçon suivant en guise de connexion de VNET 4. Vous devez également configurer un itinéraire statique dans la connexion du VNET 4 pour les préfixes de branche, et indiquer le tronçon suivant vers l’adresse IP spécifique de l’appliance virtuelle réseau dans le VNET 4.

   * **Association :** sélectionnez tous les réseaux virtuels 1, 2 et 3. Cela implique que les connexions de réseau virtuel 1, 2 et 3 seront associées à cette table de routage et pourront apprendre des itinéraires statiques et dynamiques à partir de la propagation dans cette table de routage.

   * **Propagation :** les connexions propagent des itinéraires vers des tables de routage. La sélection des réseaux virtuels 1, 2 et 3 permet de propager les itinéraires des réseaux virtuels 1, 2 et 3 vers cette table d’itinéraires. Il n’est pas nécessaire de propager les itinéraires des connexions de branche vers RT_V2B, car le trafic de réseau virtuel de branche transite par l’appliance virtuelle réseau dans VNET4.
  
1. Modifiez la table de routage par défaut **DefaultRouteTable**.

   Toutes les connexions VPN, ExpressRoute et VPN utilisateur sont associées à la table de routage par défaut. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires vers le même ensemble de tables de routage.

   * **Itinéraires :** ajoutez une entrée d’itinéraire statique agrégé pour les réseaux virtuels 1, 2 et 3 (10.1.0.0/16 dans la **Figure 2**) avec un tronçon en guise de connexion de VNET 4. Vous devez également configurer un itinéraire statique dans la connexion du VNET 4 pour les préfixes agrégés VNET 1, 2 et 3, et indiquer le tronçon suivant vers l’adresse IP spécifique de l’appliance virtuelle réseau dans le VNET 4.

   * **Association :** assurez-vous que l’option des branches (VPN/ER/P2S) est sélectionnée, en veillant à ce que les connexions de branches locales soient associées à la *table de routage par défaut*.

   * **Propagation à partir de :** assurez-vous que l’option des branches (VPN/ER/P2S) est sélectionnée, en veillant à ce que les connexions locales propagent des itinéraires vers la *table de routage par défaut*.

**Figure 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figure 2":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
