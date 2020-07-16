---
title: 'Scénario : Isolation des réseaux virtuels'
titleSuffix: Azure Virtual WAN
description: Scénarios pour le routage - Isolation des réseaux virtuels
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567304"
---
# <a name="scenario-isolating-vnets"></a>Scénario : Isolation des réseaux virtuels

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans ce scénario, l’objectif est d’empêcher des réseaux virtuels de pouvoir en atteindre d’autres. C’est ce que l’on appelle l’isolation des réseaux virtuels. La charge de travail au sein du réseau virtuel reste isolée et n’est pas en mesure de communiquer avec d’autres réseaux virtuels, comme dans un scénario universel. Toutefois, les réseaux virtuels sont nécessaires pour atteindre toutes les branches (VPN, ER et VPN utilisateur). Dans ce scénario, toutes les connexions VPN, ExpressRoute et VPN utilisateur sont associées à la seule et même table de routage. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires dans le même ensemble de tables de routage. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Workflow du scénario

Pour configurer ce scénario, prenez en compte les étapes suivantes :

1. Créez une table de routage personnalisée. Dans l’exemple, la table de routage est **RT_VNet**. Pour créer une table de routage, consultez le [Guide pratique pour configurer le routage de hub virtuel](how-to-virtual-hub-routing.md). Pour plus d’informations sur les tables de routage, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
2. Lorsque vous créez la table de routage **RT_VNet**, configurez les paramètres suivants :

   * **Association** : Sélectionnez les réseaux virtuels que vous souhaitez isoler.
   * **Propagation** : Sélectionnez l’option pour les branches, ce qui implique que les connexions de branche (VPN/ER/P2S) propageront les itinéraires vers cette table de routage.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Réseaux virtuels isolés":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
