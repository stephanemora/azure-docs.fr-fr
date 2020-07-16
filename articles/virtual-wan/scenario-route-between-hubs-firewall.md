---
title: 'Scénario : Pare-feu Azure - routage interhub'
titleSuffix: Azure Virtual WAN
description: Scénarios de routage - routage entre plusieurs hubs disposant d’un pare-feu Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567284"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Scénario : Pare-feu Azure - Interhub (préversion)

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans ce scénario, l’objectif est d’effectuer un routage entre plusieurs hubs qui contiennent le pare-feu Azure. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architecture du scénario

Dans ce scénario, nous supposons la configuration suivante :

* Vous disposez de plusieurs hubs avec le pare-feu Azure dans chaque hub.
* Vous utilisez un hub virtuel sécurisé.
* Les stratégies appropriées pour le trafic privé (VNet), Internet et le trafic de branche ont été configurées.
* Les réseaux de réseau virtuel à réseau virtuel (V2I), de réseau virtuel à succursale (V2B), de branche à réseau virtuel (B2V), passent par le pare-feu Azure dans chaque hub.

Considérations supplémentaires :

* Dans le cas d’un scénario interhub avec pare-feu Azure, l’hypothèse est que les réseaux virtuels spokes ne sont pas associés à des tables de routage distinctes (par exemple, **réseau virtuel 1** associé à **table de routage A**et **réseau virtuel 2** associés à **table de routage B**). Toutes les réseaux virtuels s’associent à la même table de routage dans laquelle résident les itinéraires du pare-feu Azure.
* La sécurisation via le pare-feu Azure est actuellement limitée à **branche <-> réseau virtuel** et au trafic **Internet** uniquement. Le flux de branche à branche via le pare-feu Azure n’est actuellement pas pris en charge.

**Figure 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="architecture":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Workflow du scénario

Pour configurer ce scénario, prenez en compte les étapes suivantes :

### <a name="step-1"></a><a name="step-1"></a>Étape 1

En supposant que vous avez déjà sécurisé les connexions via Azure Firewall Manager, la première étape consiste à s’assurer que toutes les connexions de branche et de réseau virtuel propagent **None**. Cela est nécessaire pour garantir que le trafic est défini via le pare-feu Azure.

1. Sélectionnez le hub et modifiez la table de routage **None**.
1. Mettez à jour la **Propagation** pour sélectionner toutes les branches et toutes les réseaux virtuels.

### <a name="step-2"></a><a name="step-2"></a>Étape 2

Configurez une table de routage personnalisée par hub.

1. Pour le **Hub 1**, créez une table de routage **RT_Hub1**.

    * Si vous avez utilisé Azure Firewall Manager, il crée automatiquement un itinéraire pour 0.0.0.0/0 avec le tronçon suivant **AZFW1** dans la table de routage par défaut du hub. Nous modifierons ce paramètre à l’étape 3. Pour **RT_Hub1**, créez une entrée pour 0.0.0.0/0 explicitement avec le tronçon suivant **AZFW1**. Ce paramètre active V2V, B2V et V2I via AZFW1.
    * Étant donné que vous souhaitez que les branches et réseaux virtuels du **Hub 2** soient accessibles via **AZFW2** à partir du **Hub 1** (au lieu de via AZFW1), vous devez ajouter deux autres itinéraires agrégés pour les branches (10.5.0.0/16-> AZFW2) et réseaux virtuels (10.2.0.0/16-> AZFW2) du **Hub 2**.

1. Pour le **Hub 2**, créez une table de routage **RT_Hub2**.

    * Si vous avez utilisé Azure Firewall Manager, il crée automatiquement un itinéraire pour 0.0.0.0/0 avec le tronçon suivant **AZFW2** dans la table de routage par défaut du hub. Nous modifierons ce paramètre à l’étape 3. Pour **RT_Hub2**, créez une entrée pour 0.0.0.0/0 explicitement avec le tronçon suivant **AZFW2**. Ce paramètre active V2V, B2V et V2I via **AZFW2**.
    * Étant donné que vous souhaitez que le trafic interhub soit protégé par l’**AZFW2**du Hub 2, vous n’avez pas besoin d’ajouter explicitement une étape similaire au deuxième élément de l’étape précédente du Hub 1.

### <a name="step-3"></a><a name="step-3"></a>Étape 3

Modifiez la **table de routage par défaut** dans chacun des hubs afin d’ajouter un itinéraire statique pour les flux de **la branche vers le réseau virtuel** (B2V) et de **la branche vers Internet** (B2I) via le pare-feu Azure. Le branche à branche n’est actuellement pas pris en charge via le pare-feu Azure.

1. Pour la **table de routage par défaut du Hub 1** :

    * **Branchement aux branches du Hub 2 via AZFW2** : 10.5.0.0/16->AZFW2. Cette configuration définit un itinéraire pour le pare-feu du Hub 2.
    * **Branchement aux réseaux virtuels du Hub 2 via AZFW2** : 10.2.0.0/16->AZFW2. Cette configuration définit un itinéraire pour le pare-feu du Hub 2.
    * **Branchement à une branche (locale)/branchement à un réseau virtuel (local)/branchement à Internet** : 0.0.0.0/0->AZFW1.

2. Pour la **table de routage par défaut du Hub 2** :

    * Branchement à une branche (locale et distante)/branchement à un réseau virtuel (local et distant)/branchement à Internet : 0.0.0.0/0->AZFW2.

Cela entraîne des modifications de configuration de routage, comme illustré dans la figure ci-dessous.

**Figure 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="workflow":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
* Pour plus d’informations sur la configuration du routage de hub virtuel, consultez [Guide pratique pour configurer le routage de hub virtuel](how-to-virtual-hub-routing.md).
