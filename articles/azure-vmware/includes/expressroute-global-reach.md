---
title: Connectivité et réseau Azure VMware Solution
description: Description de la connectivité et du réseau Azure VMware Solution.
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 569beec244e2ccf80aae3e4bb3f7a3e2cf65980a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070720"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) permet de connecter des clouds privés à des environnements locaux. Dans la mesure où il connecte les circuits directement au niveau de MSEE (Microsoft Enterprise Edge), la connexion nécessite un réseau virtuel (VNet) avec un circuit ExpressRoute en local dans votre abonnement.  En effet, les passerelles VNet (passerelles ExpressRoute) ne peuvent pas faire transiter le trafic, ce qui signifie que vous pouvez attacher deux circuits à la même passerelle mais que cela n’entraînera pas l’envoi du trafic d’un circuit à l’autre.

>[!NOTE]
>Pour les emplacements où ExpressRoute Global Reach n’est pas activé, par exemple en raison de réglementations locales, vous devez créer une solution de routage à l’aide de machines virtuelles Azure IaaS. Pour obtenir des exemples, consultez [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking).

