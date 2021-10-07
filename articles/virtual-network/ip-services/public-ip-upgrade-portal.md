---
title: Mettre à niveau une adresse IP publique - Portail Azure
description: Dans cet article, vous apprendrez à mettre à niveau l'adresse IP publique d'une référence SKU de base à l'aide du portail Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: f436cd2d113909a65b62891f9fbfb5965d06a981
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368514"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-portal"></a>Mettre à niveau une adresse IP publique à l'aide du portail Azure

Les adresses IP publiques Azure sont créées avec une référence SKU de base ou standard. La référence SKU détermine la fonctionnalité, ainsi que la méthode d'allocation, la prise en charge des fonctionnalités et les ressources auxquelles elles peuvent être associées. 

Dans cet article, vous allez apprendre à mettre à niveau l'adresse IP publique d'une référence SKU de base vers une référence SKU standard à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Une adresse IP publique de référence SKU de base statique dans votre abonnement. Pour plus d'informations, consultez [Créer une adresse IP publique - Portail Azure](../../virtual-network/create-public-ip-portal.md#create-a-basic-sku-public-ip-address).

## <a name="upgrade-public-ip-address"></a>Mettre à niveau une adresse IP publique

Dans cette section, vous allez vous connecter au portail Azure et mettre à niveau l'adresse IP publique de votre référence SKU de base vers la référence SKU standard.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

3. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

4. Dans **Adresses IP publiques**, sélectionnez **myBasicPublicIP** ou l'adresse IP que vous souhaitez mettre à niveau.

5. Sélectionnez la bannière de mise à niveau en haut de la section Vue d'ensemble dans **myBasicPublicIP**.

    :::image type="content" source="./media/public-ip-upgrade-portal/upgrade-ip-portal.png" alt-text="Mettre à niveau l'adresse IP de base sur le portail Azure" border="true":::

    > [!NOTE]
    > L'adresse IP publique de base que vous mettez à niveau doit être définie sur le type d'allocation statique. Vous recevrez un avertissement indiquant que l'adresse IP ne peut pas être mise à niveau si vous essayez de mettre à niveau une adresse IP allouée dynamiquement.

6.  Cochez la case **J'accepte**. Sélectionnez **Mettre à niveau**.

    > [!WARNING]
    > La mise à niveau d'une adresse IP publique de base vers la référence SKU standard ne peut pas être inversée. Les adresses IP publiques mises à niveau de la référence SKU de base vers la référence standard n'ont toujours pas de [zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) garantie.

## <a name="verify-upgrade"></a>Vérifier la mise à niveau

Dans cette section, vous allez vérifier que l'adresse IP publique est désormais celle de la référence SKU standard.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

3. Dans les résultats de la recherche, sélectionnez **Adresse IP publique**.

4. Dans **Adresses IP publiques**, sélectionnez **myBasicPublicIP** ou l'adresse IP que vous avez mise à niveau.

5. Vérifiez que la référence SKU est répertoriée comme **Standard** dans la section **Vue d'ensemble**.

    :::image type="content" source="./media/public-ip-upgrade-portal/verify-upgrade-ip.png" alt-text="Vérifier que l'adresse IP publique est celle de la référence SKU standard." border="true":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez mis à niveau l'adresse IP publique d'une référence SKU de base vers la référence SKU standard.

Pour plus d'informations, sur les adresses IP publiques dans Azure, consultez :

- [Adresses IP publiques dans Azure](public-ip-addresses.md)
- [Créer une adresse IP publique - Portail Azure](../../virtual-network/create-public-ip-portal.md)

