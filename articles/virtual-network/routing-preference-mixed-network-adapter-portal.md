---
title: Configurer les deux options de préférence de routage pour une machine virtuelle – Portail Azure
description: Découvrir comment activer les deux choix de préférence de routage
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101678390"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Configurer les deux options de préférence de routage pour une machine virtuelle

Cet article explique comment configurer les deux options de [préférence de routage](./routing-preference-overview.md) (Internet et le réseau Microsoft mondial) pour une machine virtuelle. Pour cela, vous devez utiliser deux interfaces réseau virtuelles : une interface réseau avec une adresse IP publique routée via le réseau Microsoft mondial, et l’autre avec une IP publique routée via un réseau de fournisseur de services Internet.

## <a name="prerequisites"></a>Prérequis

Créez une machine virtuelle avec une adresse IP publique en suivant les instructions de la section [Créer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure](./virtual-network-deploy-static-pip-arm-portal.md). Le choix de la préférence de routage par défaut pour l’adresse IP publique s’effectue via le **réseau Microsoft mondial**. Lorsque vous avez créé une machine virtuelle avec une adresse IP publique, ajoutez une deuxième IP publique à la machine virtuelle qui route le trafic, via le réseau du fournisseur de services Internet de transit, avec ses préférences de routage configurées sur **Internet**.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Créer une adresse IP publique en choisissant Internet comme préférence de routage
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource**. 
3. Dans la zone de recherche, tapez *Adresse IP publique*.
4. Dans les résultats de recherche, sélectionnez **Adresse IP publique**. Ensuite, dans la page **Adresse IP publique**, sélectionnez **Créer**.
5. Dans les options de **Préférence de routage**, sélectionnez **Internet**.

      ![Créez une adresse IP publique](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Les adresses IP publiques sont créées avec une adresse IPv4 et IPv6. Toutefois, actuellement, la préférence de routage ne prend en charge qu’IPV4.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Créer une interface réseau et associer l’IP publique

1. Créez une [interface réseau](./routing-preference-overview.md) à l’aide du portail Azure avec les paramètres par défaut. 
1. [Associez l’IP publique à l’interface réseau](./associate-public-ip-address-vm.md) créée à la section précédente. L’affichage d’une adresse IP peut prendre quelques secondes. Affichez l’adresse IP publique affectée à la configuration IP, comme illustré :

    ![Associer l’IP publique](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Attacher l’interface réseau à la machine virtuelle

1. [Attachez l’interface réseau créée dans la section précédente à la machine virtuelle](./virtual-network-network-interface-vm.md).
2. Vous pouvez désormais afficher deux interfaces de réseau virtuel associées à la machine virtuelle, l’une avec une adresse IP publique routée via le réseau Microsoft mondial, et l’autre routée via le réseau d’un fournisseur de services Internet, comme illustré : ![Attacher une interface réseau à une machine virtuelle](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur l’[adresse IP publique avec préférence de routage](routing-preference-overview.md).
- [Configurer la préférence de routage pour une machine virtuelle](tutorial-routing-preference-virtual-machine-portal.md).
- [Configurer une préférence de routage pour une adresse IP publique à l’aide de PowerShell](routing-preference-powershell.md).
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).