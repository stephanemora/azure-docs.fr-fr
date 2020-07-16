---
title: 'Scénario : Isolation personnalisée pour réseaux virtuels'
titleSuffix: Azure Virtual WAN
description: Scénarios de routage - empêcher les réseaux virtuels sélectionnés de pouvoir s’atteindre mutuellement
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567308"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scénario : Isolation personnalisée pour réseaux virtuels

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans un scénario d’isolation personnalisée pour réseaux virtuels, l’objectif est d’empêcher un ensemble spécifique de réseaux virtuels de pouvoir atteindre d’autres ensembles de réseaux virtuels spécifiques. Toutefois, les réseaux virtuels sont nécessaires pour atteindre toutes les branches (VPN/ER/VPN utilisateur).

Dans ce scénario, les connexions VPN, ExpressRoute et VPN utilisateur (collectivement appelées branches) sont associées à la même table de routage (table de routage par défaut). Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires dans le même ensemble de tables de routage. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Workflow du scénario

Dans **figure 1**, il y a des connexions de réseaux virtuels en bleu et en rouge.

* Les réseaux virtuels connectés en bleu peuvent s’atteindre entre eux, ainsi qu’atteindre toutes les connexions de branches (VPN/ER/P2S).
* Les réseaux virtuels connectés en rouge peuvent s’atteindre entre eux, ainsi qu’atteindre toutes les connexions de branches (VPN/ER/P2S).

Suivez les étapes ci-dessous lors de la configuration du routage.

1. Créez deux tables de routage personnalisées dans le Portail Azure, **RT_BLUE** et **RT_RED**.
2. Pour la table de routage **RT_BLUE**, sous :
   * **Association** : Sélectionner tous les réseaux virtuels en bleu
   * **Propagation** : Pour les branches, sélectionnez l’option pour les branches, ce qui implique que les connexions de branche (VPN/ER/P2S) propageront les itinéraires vers cette table de routage.
3. Répétez les mêmes étapes pour la table de routage **RT_RED** pour les réseaux virtuels en rouge et les branches (VPN/ER/P2S).

Cela entraînera des modifications de configuration de routage, comme illustré dans la figure ci-dessous

**Figure 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="figure 1":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
