---
title: Mettre à niveau un réseau Azure Virtual WAN de base vers le type Standard - Portail Azure | Microsoft Docs
description: Vous pouvez mettre à niveau le type de votre réseau étendu virtuel pour bénéficier de fonctionnalités supplémentaires.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 769aa6c0d546b7a9bcbe355136bad811fb4f47b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753303"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Mettre à niveau un réseau étendu virtuel de base vers le type Standard

Cet article a pour but de vous aider à mettre à niveau un réseau étendu de base vers le type Standard. Un type de réseau étendu « De base » applique à tous les hubs qu'il héberge la référence SKU De base. Dans un hub De base, vous êtes limité à la fonctionnalité VPN site à site. Un type de réseau étendu « Standard » applique à tous les hubs qu'il héberge la référence SKU Standard. Lorsque vous utilisez des hubs Standard, vous pouvez activer ExpressRoute, un VPN utilisateur (point à site), un hub à maillage complet et le transit de réseau virtuel à réseau virtuel via les hubs Azure.

Le tableau suivant présente les configurations disponibles pour chaque type de réseau étendu :

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Pour modifier le type de réseau étendu virtuel

1. Sur la page de votre réseau étendu virtuel, sélectionnez **Configuration** pour ouvrir la page Configuration.

   ![Diagramme WAN virtuel](./media/upgrade-virtual-wan/1.png)
2. Pour le type de réseau étendu virtuel, sélectionnez **Standard** dans la liste déroulante.

   ![Diagramme WAN virtuel](./media/upgrade-virtual-wan/2.png)
3. Sachez que si vous procédez à une mise à niveau vers un réseau étendu virtuel Standard, vous ne pourrez pas revenir à un réseau étendu virtuel De base. Sélectionnez **Confirmer** si vous souhaitez procéder à la mise à niveau.

   ![Diagramme WAN virtuel](./media/upgrade-virtual-wan/4.png)
4. Une fois la modification enregistrée, la page de votre réseau étendu virtuel est semblable à l'exemple suivant.

   ![Diagramme WAN virtuel](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).