---
title: Connectivité et réseau Azure VMware Solution
description: Description de la connectivité et du réseau Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: add146bb5b64e12b09bec89c080e1222154d4e2f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638495"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) permet de connecter des clouds privés à des environnements locaux. Il connecte les circuits directement au niveau de Microsoft Enterprise Edge (MSEE). La connexion demande un réseau virtuel (vNet) avec un circuit ExpressRoute local dans votre abonnement.  En effet, les passerelles VNet (passerelles ExpressRoute) ne peuvent pas faire transiter le trafic, ce qui signifie que vous pouvez attacher deux circuits à la même passerelle mais que cela n’entraînera pas l’envoi du trafic d’un circuit à l’autre.

Chaque environnement Azure VMware Solution est sa propre région ExpressRoute (son propre appareil MSEE virtuel), qui vous permet de connecter Global Reach à l’emplacement d’appairage « local ».  Il vous permet de connecter plusieurs instances Azure VMware Solution d’une région au même emplacement d’appairage. 

>[!NOTE]
>Pour les emplacements où ExpressRoute Global Reach n’est pas activé, par exemple en raison de réglementations locales, vous devez créer une solution de routage à l’aide de machines virtuelles Azure IaaS. Pour obtenir des exemples, consultez [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking).

