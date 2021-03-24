---
title: Configurer une préférence de routage pour une adresse IP publique – Portail Azure
description: Découvrez comment créer une adresse IP publique avec une préférence de routage du trafic Internet
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
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672902"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Configurer une préférence de routage pour une adresse IP publique à l’aide du portail Azure

Cet article montre comment configurer une [préférence de routage](./routing-preference-overview.md) via un réseau de fournisseur de services Internet (option **Internet**) pour une adresse IP publique à l’aide du portail Azure. Une fois l’adresse IP publique créée, vous pouvez l’associer aux ressources Azure suivantes pour les trafics entrant et sortant via Internet :

* Machine virtuelle
* Jeu de mise à l’échelle de machine virtuelle
* Azure Kubernetes Service (AKS)
* Équilibreur de charge accessible via Internet
* Application Gateway
* Pare-feu Azure

Par défaut, la préférence de routage pour l’adresse IP publique est définie sur le réseau Microsoft mondial pour tous les services Azure, et peut être associée à n’importe lequel de ceux-ci.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Créer une adresse IP publique avec une préférence de routage
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource**.
3. Dans la zone de recherche, tapez *Adresse IP publique*.
3. Dans les résultats de recherche, sélectionnez **Adresse IP publique**. Ensuite, dans la page **Adresse IP publique**, sélectionnez **Créer**.
1. En guise de SKU, sélectionnez **Standard**.
1. Pour **Préférence de routage**, sélectionnez **Internet**.

      ![Créez une adresse IP publique](./media/routing-preference-portal/public-ip-new.png)
1. Dans la section **Configuration de l’adresse IP IPv4**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Value |
    | ------- | ----- |
    | Abonnement | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer**, entrez *RoutingPreferenceResourceGroup* et sélectionnez **OK**. |
    | Emplacement | Sélectionnez **USA Est**.|
    | Zone de disponibilité | Conservez la valeur par défaut : **Redondant interzone**. |
1. Sélectionnez **Create** (Créer).

    > [!NOTE]
    > Les adresses IP publiques sont créées avec une adresse IPv4 et IPv6. Toutefois, actuellement, la préférence de routage ne prend en charge qu’IPV4.

Vous pouvez associer l’adresse IP publique créée ci-dessus à une machine virtuelle [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Utilisez la section CLI sur la page du tutoriel : [Associez une adresse IP publique à une machine virtuelle](associate-public-ip-address-vm.md#azure-cli) pour associer l’adresse IP publique à votre machine virtuelle. Vous pouvez associer l’adresse IP publique créée ci-dessus avec un [équilibreur de charge Azure](../load-balancer/load-balancer-overview.md) en l’assignant à la configuration **frontale** de l’équilibreur de charge. L’adresse IP publique sert d’adresse IP virtuelle (VIP) à charge équilibrée.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur l’[adresse IP publique avec préférence de routage](routing-preference-overview.md).
- [Configurer la préférence de routage pour une machine virtuelle](tutorial-routing-preference-virtual-machine-portal.md).
- [Configurer une préférence de routage pour une adresse IP publique à l’aide de PowerShell](routing-preference-powershell.md).
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).