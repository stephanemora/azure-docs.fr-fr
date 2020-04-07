---
title: 'Configurer une stratégie IPsec personnalisée pour Azure Virtual WAN : Portail | Microsoft Docs'
description: Découvrez comment configurer une stratégie IPsec personnalisée pour Azure Virtual WAN à l’aide du portail.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73511178"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configurer une stratégie IPsec personnalisée pour Virtual WAN à l’aide du portail

Vous pouvez configurer une stratégie IPsec personnalisée pour Virtual WAN dans le portail Azure. Les stratégies personnalisées sont utiles si vous souhaitez que les deux côtés (la passerelle locale et la passerelle VPN Azure) utilisent les mêmes paramètres pour IKE Phase 1 et IKE Phase 2.

## <a name="working-with-custom-policies"></a>Utilisation de stratégies personnalisées

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configurer une stratégie

1. **Localisez le hub virtuel**. Dans un navigateur, accédez au [portail Azure](https://aka.ms/azurevirtualwanpreviewfeatures) et connectez-vous avec votre compte Azure. Localisez le hub virtuel de votre site.
2. **Sélectionnez le site VPN**. Dans la page du hub, sélectionnez le site VPN pour lequel vous voulez configurer une stratégie personnalisée.

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Modifiez la connexion VPN**. Dans le **Menu contextuel** **…** , sélectionnez **Modifier la connexion VPN**.

   ![modifier](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configurez les paramètres**. Dans la page **Modifier la connexion VPN**, configurez les paramètres. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

   ![configurer et enregistrer](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).