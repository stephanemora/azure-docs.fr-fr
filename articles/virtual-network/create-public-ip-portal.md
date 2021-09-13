---
title: Créer une adresse IP publique - Portail Azure
titleSuffix: Azure Virtual Network
description: Apprendre à créer une adresse IP publique à l’aide du portail Azure
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/04/2021
ms.author: allensu
ms.openlocfilehash: 6cf9e86222d98835ea5355440343df96794dbfd4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435050"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Créer une adresse IP publique à l’aide du portail Azure

Cet article explique comment créer une ressource d’adresse IP publique à l’aide du portail Azure. 

Pour plus d’informations sur les ressources auxquelles cette adresse IP publique peut être associée et la différence entre les références SKU de base et standard, consultez la section [Adresses IP publiques](./public-ip-addresses.md). 

## <a name="create-a-standard-sku-public-ip-address"></a>Créer une adresse IP publique de référence SKU standard

Effectuez les étapes suivantes pour créer une adresse IPv4 publique standard nommée **myStandardPublicIP**.  

> [!NOTE]
>Pour créer une adresse IPv6, choisissez **IPv6** pour le paramètre **Version IP**. Si votre déploiement nécessite une configuration à double pile (adresse IPv4 et IPv6), choisissez **Les deux**.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

3. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

4. Sélectionnez **+ Créer**.

5. Dans **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes :

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4              |    
    | SKU                     | sélectionnez **Standard**.         |
    | Niveau                   | Sélectionnez **Régional**    </br> Pour plus d’informations, consultez [Préférence de routage et niveau](#routing-preference-and-tier).     |
    | Nom                    | Entrez **myStandardPublicIP**          |
    | Affectation d’adresses IP   | Verrouillée sur **Statique**                |
    | Préférence de routage     | Sélectionnez **Réseau Microsoft**. </br> Pour plus d’informations, consultez [Préférence de routage et niveau](#routing-preference-and-tier). |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.        |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement   |
    | Resource group          | Sélectionnez **Créer** et entrez **myResourceGroup**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **(États-Unis) USA Est 2**.     |
    | Zone de disponibilité       | Sélectionnez **Aucune zone**. |

6. Sélectionnez **Create** (Créer).

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Créer une adresse IP standard dans le portail Azure" border="false":::

> [!NOTE]
> Dans les régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant interzone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

> [!NOTE]
> IPv6 n’est actuellement pas pris en charge avec la préférence de routage ou l’équilibrage de charge interrégional (niveau Global).

## <a name="create-a-basic-sku-public-ip-address"></a>Créer une adresse IP publique de référence SKU de base

Dans cette section, créez une adresse IPv4 publique de base nommée **myBasicPublicIP**. 

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
    | Nom                    | Entrez **myBasicPublicIP**          |
    | Affectation d’adresses IP   | Sélectionnez **Statique**.            |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.       |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement.   |
    | Resource group          | Sélectionnez **Créer** et entrez **myResourceGroup**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **(États-Unis) USA Est 2**.      |

6. Sélectionnez **Create** (Créer).

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Créer une adresse IP de base dans le portail Azure" border="false":::

S’il est acceptable que l’adresse IP change au fil du temps, l’affectation **dynamique** d’adresses IP peut être sélectionnée en changeant la méthode d’allocation (AllocationMethod) pour **Dynamique**. 

---

## <a name="routing-preference-and-tier"></a>Préférence de routage et niveau

Les adresses IPv4 publiques statiques de référence SKU standard prennent en charge la préférence de routage ou la fonctionnalité Niveau global. Choisissez la préférence de routage et le niveau lors de la création d’une nouvelle adresse IP publique.

### <a name="routing-preference"></a>Préférence de routage

Par défaut, la préférence de routage pour les adresses IP publiques est définie sur le « Réseau Microsoft », qui remet le trafic à l’utilisateur via le réseau étendu mondial de Microsoft.  

La sélection de **Internet** réduit les déplacements sur le réseau de Microsoft en utilisant à la place le réseau du fournisseur de services Internet pour acheminer le trafic à un coût optimisé.  

Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](./routing-preference-overview.md).

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Configurer la préférence de routage dans le portail Azure" border="false":::

### <a name="tier"></a>Niveau

Les adresses IP publiques sont associées à une seule région. Le niveau **Global** s’étend sur une adresse IP dans plusieurs régions. Le niveau **Global** est exigé pour les front-ends des équilibreurs de charge interrégionaux.  

Pour plus d’informations, consultez [Équilibreur de charge interrégional](../load-balancer/cross-region-overview.md).

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Configurer le niveau dans le portail Azure" border="false":::

## <a name="additional-information"></a>Informations supplémentaires 

Pour plus d’informations sur les champs répertoriés ci-dessus, consultez la section [Gérer les adresses IP publiques](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Étapes suivantes
- Associer une [adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-portal)
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).