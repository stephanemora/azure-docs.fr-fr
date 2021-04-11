---
title: Créer une adresse IP publique - Portail Azure
description: Découvrez comment créer une adresse IP publique à l’aide du portail Azure
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: c1ac3f2fa1ef3f1a24077064ad3ad3f3c30c5f3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048355"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Créer une adresse IP publique à l’aide du portail Azure

Cet article explique comment créer une ressource d’adresse IP publique à l’aide du portail Azure. 

Pour plus d’informations sur les ressources auxquelles cette adresse IP publique peut être associée et la différence entre les références SKU de base et standard, consultez la section [Adresses IP publiques](./public-ip-addresses.md). 

Cet article concerne les adresses IPv4. Pour plus d’informations sur les adresses iPv6; consultez la section [IPv6 pour Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Référence Standard**](#tab/option-create-public-ip-standard-zones)

Effectuez les étapes suivantes pour créer une adresse IP publique redondante interzone standard nommée **myStandardZRPublicIP**.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource**. 
3. Dans la zone de recherche, tapez **adresse IP publique**. Sélectionnez **Adresse IP publique** dans les résultats de recherche.
4. Sur la page **Adresse IP publique**, sélectionnez **Créer**.
5. Sur la page **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4                 |    
    | SKU                     | sélectionnez **Standard**.         |
    | Niveau*                   | Sélectionnez **Régional**         |
    | Nom                    | Entrez **myStandardZRPublicIP**          |
    | Affectation d’adresses IP   | Notez que cette sélection est verrouillée sur « Statique »                                        |
    | Préférence de routage      | Laissez la valeur par défaut **Microsoft network**. </br> Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](./routing-preference-overview.md). |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.        |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement.   |
    | Resource group          | Sélectionnez **Créer** et entrez **myResourceGroup**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **USA Est 2**      |
    | Zone de disponibilité       | Sélectionnez **Redondant interzone**, Aucune zone, ou choisissez une zone spécifique (voir la remarque ci-dessous) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Créer une adresse IP dans le portail Azure" border="false":::

> [!NOTE]
> Ces sélections sont valides dans les régions avec [Zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones). </br>
Vous pouvez sélectionner une zone spécifique dans ces régions, mais elle ne sera pas résiliente aux échecs de zone. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

\* = Le niveau se rapporte à la fonctionnalité [Équilibreur de charge interrégion](../load-balancer/cross-region-overview.md), actuellement en préversion.

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-create-public-ip-basic)

Dans cette section, créez une adresse IP publique de base nommée **myBasicPublicIP**. 

> [!NOTE]
> Les adresses IP publiques de base ne prennent pas en charge les zones de disponibilité.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource**. 
3. Dans la zone de recherche, tapez **adresse IP publique**. Sélectionnez **Adresse IP publique** dans les résultats de recherche.
4. Sur la page **Adresse IP publique**, sélectionnez **Créer**.
5. Sur la page **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4                 |    
    | SKU                     | Sélectionnez **De base**         |
    | Nom                    | Entrez *myBasicPublicIP*          |
    | Affectation d’adresses IP   | Sélectionnez **Statique** (voir la remarque ci-dessous)                                     |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.       |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement.   |
    | Resource group          | Sélectionnez **Créer** et entrez **myResourceGroup**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **USA Est 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Créer une adresse IP standard dans le portail Azure" border="false":::

S’il est acceptable que l’adresse IP change au fil du temps, l’affectation d’adresse IP **Dynamique** peut être sélectionnée.

---

## <a name="additional-information"></a>Informations supplémentaires 

Pour plus d’informations sur les champs répertoriés ci-dessus, consultez la section [Gérer les adresses IP publiques](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Étapes suivantes
- Associer une [adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-portal)
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).