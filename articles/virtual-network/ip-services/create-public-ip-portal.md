---
title: 'Démarrage rapide : Créer une adresse IP publique – Portail Azure'
titleSuffix: Azure Virtual Network
description: Dans ce guide de démarrage rapide, apprenez à créer une adresse IP publique de SKU Standard ou De base. Vous allez également découvrir la préférence de routage et le niveau.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: d45e48120c69eeb41ec8bf741e32437f77d2ec69
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225021"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Démarrage rapide : Créer une adresse IP publique à l’aide du portail Azure

Dans ce guide de démarrage rapide, vous allez apprendre à créer une adresse IP publique Azure. Les adresses IP publiques dans Azure sont utilisées pour les connexions publiques aux ressources Azure. Les adresses IP publiques sont disponibles dans deux SKU : de base et standard. Deux niveaux d’adresses IP publiques sont disponibles : régional et mondial.  La préférence de routage d’une adresse IP publique est définie lors de sa création. Le routage Internet et le routage Réseau Microsoft sont les choix disponibles.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Référence Standard**](#tab/option-1-create-public-ip-standard)

>[!NOTE]
>L’adresse IP publique de SKU standard est recommandée pour les charges de travail de production.  Pour plus d’informations sur les SKU, consultez **[Adresses IP publiques](public-ip-addresses.md)** .

## <a name="create-a-standard-sku-public-ip-address"></a>Créer une adresse IP publique de référence SKU standard

Effectuez les étapes suivantes pour créer une adresse IPv4 publique standard nommée **myStandardPublicIP**. 

> [!NOTE]
>Pour créer une adresse IPv6, choisissez **IPv6** pour le paramètre **Version IP**. Si votre déploiement nécessite une configuration à double pile (adresse IPv4 et IPv6), choisissez **Les deux**.

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

3. Sélectionnez **+ Créer**.

4. Dans **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes :

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4              |    
    | SKU                     | sélectionnez **Standard**.         |
    | Niveau                   | Sélectionnez **Régional**     |
    | Nom                    | Entrez **myStandardPublicIP**          |
    | Affectation d’adresses IP   | Verrouillée sur **Statique**                |
    | Préférence de routage     | Sélectionnez **Réseau Microsoft**. |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.        |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement   |
    | Groupe de ressources          | Sélectionnez **Créer** et entrez **QuickStartCreateIP-rg**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **(États-Unis) USA Est 2**.     |
    | Zone de disponibilité       | Sélectionnez **Aucune zone**. |

5. Sélectionnez **Create** (Créer).

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Capture d’écran de la création d’une adresse IP standard dans le portail Azure" border="false":::

> [!NOTE]
> Dans les régions avec des [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant dans une zone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../../availability-zones/az-overview.md).

Vous pouvez associer l’adresse IP publique créée ci-dessus à une machine virtuelle [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Utilisez la section CLI sur la page du tutoriel : [Associez une adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-cli) pour associer l’adresse IP publique à votre machine virtuelle. Vous pouvez associer l’adresse IP publique créée ci-dessus avec un [équilibreur de charge Azure](../../load-balancer/load-balancer-overview.md) en l’assignant à la configuration **frontale** de l’équilibreur de charge. L’adresse IP publique sert d’adresse IP virtuelle (VIP) à charge équilibrée.

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-1-create-public-ip-basic)

>[!NOTE]
>L’adresse IP publique de SKU standard est recommandée pour les charges de travail de production.  Pour plus d’informations sur les SKU, consultez **[Adresses IP publiques](public-ip-addresses.md)** .

## <a name="create-a-basic-sku-public-ip-address"></a>Créer une adresse IP publique de référence SKU de base

Dans cette section, créez une adresse IPv4 publique de base nommée **myBasicPublicIP**. 

> [!NOTE]
> Les adresses IP publiques de base ne prennent pas en charge les zones de disponibilité.

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

3. Sélectionnez **+ Créer**.

4. Sur la page **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez **IPv4**                |    
    | SKU                     | Sélectionnez **De base**         |
    | Nom                    | Entrez **myBasicPublicIP**          |
    | Affectation d’adresses IP   | Sélectionnez **Statique**.            |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.       |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement.   |
    | Groupe de ressources          | Sélectionnez **Créer** et entrez **QuickStartCreateIP-rg**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **(États-Unis) USA Est 2**.      |

5. Sélectionnez **Create** (Créer).

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Capture d’écran de la création d’une adresse IP de base dans le portail Azure" border="true":::

S’il est acceptable que l’adresse IP change au fil du temps, l’affectation **dynamique** d’adresses IP peut être sélectionnée en changeant la méthode d’allocation (AllocationMethod) pour **Dynamique**. 

# <a name="routing-preference"></a>[**Préférence de routage**](#tab/option-1-create-public-ip-routing-preference)

Cette section montre comment configurer une [préférence de routage](routing-preference-overview.md) via un réseau de fournisseur de services Internet (option **Internet**) pour une adresse IP publique. Après avoir créé l’adresse IP publique, vous pouvez l’associer aux ressources Azure suivantes :

* Machine virtuelle
* Jeu de mise à l’échelle de machine virtuelle
* Azure Kubernetes Service (AKS)
* Équilibreur de charge accessible via Internet
* Application Gateway
* Pare-feu Azure

Par défaut, la préférence de routage pour l’adresse IP publique est définie sur le réseau Microsoft mondial pour tous les services Azure, et peut être associée à n’importe lequel de ceux-ci.

> [!NOTE]
>Pour créer une adresse IPv6, choisissez **IPv6** pour le paramètre **Version IP**. Si votre déploiement nécessite une configuration à double pile (adresse IPv4 et IPv6), choisissez **Les deux**.

## <a name="create-a-public-ip-with-internet-routing"></a>Créer une IP publique dotée du routage Internet

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

3. Sélectionnez **+ Créer**.

4. Dans **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes :

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4              |    
    | SKU                     | sélectionnez **Standard**.         |
    | Niveau                   | Sélectionnez **Régional**     |
    | Nom                    | Entrez **myStandardPublicIP-RP**          |
    | Affectation d’adresses IP   | Verrouillée sur **Statique**                |
    | Préférence de routage     | Sélectionnez **Internet**. |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.        |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement   |
    | Groupe de ressources          | Sélectionnez **Créer** et entrez **QuickStartCreateIP-rg**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **(États-Unis) USA Est 2**.     |
    | Zone de disponibilité       | Sélectionnez **Redondant interzone** |

5. Sélectionnez **Create** (Créer).

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Capture d’écran de la configuration de la préférence de routage dans le portail Azure" border="true":::

> [!NOTE]
> Les adresses IP publiques sont créées avec une adresse IPv4 et IPv6. Toutefois, actuellement, la préférence de routage ne prend en charge qu’IPV4.

> [!NOTE]
> Dans les régions avec des [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant dans une zone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../../availability-zones/az-overview.md).

Vous pouvez associer l’adresse IP publique créée ci-dessus à une machine virtuelle [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Utilisez la section CLI sur la page du tutoriel : [Associez une adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-cli) pour associer l’adresse IP publique à votre machine virtuelle. Vous pouvez associer l’adresse IP publique créée ci-dessus avec un [équilibreur de charge Azure](../../load-balancer/load-balancer-overview.md) en l’assignant à la configuration **frontale** de l’équilibreur de charge. L’adresse IP publique sert d’adresse IP virtuelle (VIP) à charge équilibrée.

# <a name="tier"></a>[**Niveau**](#tab/option-1-create-public-ip-tier)

Les adresses IP publiques sont associées à une seule région. Le niveau **Global** s’étend sur une adresse IP dans plusieurs régions. Le niveau **Global** est exigé pour les front-ends des équilibreurs de charge interrégionaux.  

Pour plus d’informations, consultez [Équilibreur de charge interrégional](../../load-balancer/cross-region-overview.md).

## <a name="create-a-global-tier-public-ip"></a>Créer une IP publique de niveau mondial

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

3. Sélectionnez **+ Créer**.

4. Dans **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes :

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4              |    
    | SKU                     | sélectionnez **Standard**.         |
    | Niveau                   | Sélectionnez **Global**.     |
    | Name                    | Entrez **myStandardPublicIP-Global**          |
    | Affectation d’adresses IP   | Verrouillée sur **Statique**                |
    | Préférence de routage     | Sélectionnez **Microsoft**. |
    | Délai d’inactivité (minutes)  | Conservez la valeur par défaut **4**.        |
    | Étiquette du nom DNS          | Laissez la valeur vide.    |
    | Abonnement            | Sélectionnez votre abonnement   |
    | Groupe de ressources          | Sélectionnez **Créer** et entrez **QuickStartCreateIP-rg**. </br> Sélectionnez **OK**. |
    | Emplacement                | Sélectionnez **(États-Unis) USA Est 2**.     |
    | Zone de disponibilité       | Sélectionnez **Redondant interzone** |

5. Sélectionnez **Create** (Créer).

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Capture d’écran de la configuration du niveau dans le portail Azure" border="true":::

Vous pouvez associer l’adresse IP créée ci-dessus à un équilibreur de charge interrégional. Pour plus d’informations, consultez [Tutoriel : Créer un équilibreur de charge interrégional à l’aide du portail Azure](../../load-balancer/tutorial-cross-region-portal.md).

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez l’adresse IP publique en suivant ces étapes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.

2. Dans les résultats de la recherche, sélectionnez **Groupes de ressources**.

3. Sélectionnez **QuickStartCreateIP-rg**.

4. Sélectionnez **Supprimer le groupe de ressources**.

5. Entrez **myResourceGroup** dans **TAPER LE NOM DU GROUPE DE RESSOURCES** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un préfixe d’adresse IP publique :
> [!div class="nextstepaction"]
> [Créer un préfixe d’adresse IP publique à l’aide du portail Azure](create-public-ip-prefix-portal.md)