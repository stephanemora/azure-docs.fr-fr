---
title: Azure Defender pour DNS – Avantages et fonctionnalités
description: Découvrir les avantages et les fonctionnalités d’Azure Defender pour DNS
author: memildin
ms.author: memildin
ms.date: 10/11/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ad2b761a747401646c48dcbf32385b8e5ec153c1
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740224"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Présentation d’Azure Defender pour DNS

Azure Defender pour DNS fournit une couche de protection supplémentaire aux ressources qui utilisent la fonctionnalité de [résolution de noms Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) d’Azure DNS. 

Dans Azure DNS, Defender pour DNS supervise les requêtes provenant de ces ressources et détecte les activités suspectes sans avoir besoin d’agents supplémentaires sur vos ressources.

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|**Azure Defender pour DNS** est facturé conformément à la [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png":::Azure Government|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Quels sont les avantages d’Azure Defender pour DNS ?

Azure Defender pour DNS détecte les activités suspectes et anormales, par exemple :

- **Exfiltration de données** à partir de vos ressources Azure via le tunneling DNS
- Communication de **programmes malveillants** avec les serveurs de commande et de contrôle
- **Attaques DNS** - Communication avec des programmes de résolution DNS malveillants 
- **Communication avec des domaines utilisés pour des activités malveillantes** telles que l’hameçonnage et le minage de cryptomonnaie

La liste complète des alertes fournie par Azure Defender pour DNS se trouve sur la [page de référence des alertes](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Les dépendances

Azure Defender pour DNS n’utilise pas d’agent. 

Afin de protéger votre couche DNS, activez Azure Defender pour DNS sur chacun de vos abonnements, comme cela est décrit dans [Activer Azure Defender](enable-azure-defender.md).


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Azure Defender pour DNS. 

> [!div class="nextstepaction"]
> [Activer Azure Defender](enable-azure-defender.md)

Pour des informations connexes, consultez l’article suivant : 

- Les alertes de sécurité peuvent être générées par Security Center, ou reçues par Security Center depuis d’autres produits de sécurité. Pour exporter toutes ces alertes vers Azure Sentinel, un système SIEM tiers ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers un système SIEM](continuous-export.md).
