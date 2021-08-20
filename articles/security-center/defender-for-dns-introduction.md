---
title: Azure Defender pour DNS – Avantages et fonctionnalités
description: Découvrir les avantages et les fonctionnalités d’Azure Defender pour DNS
author: memildin
ms.author: memildin
ms.date: 07/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 48306c8ec46274bc123ada4f7c8f748a230edf30
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690832"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Présentation d’Azure Defender pour DNS

[Azure DNS](../dns/dns-overview.md) est un service d’hébergement pour les domaines DNS, qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS à l’aide des mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure.

Azure Defender pour DNS fournit une couche supplémentaire de protection pour vos ressources qui sont connectées à Azure DNS par :

- la supervision en continu de toutes les requêtes DNS émanant de vos ressources Azure
- l’exécution de l’analytique de sécurité avancée pour vous avertir des activités suspectes

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Azure Defender pour DNS** est facturé conformément à la [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Chine<br>:::image type="icon" source="./media/icons/no-icon.png":::Azure Government|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Quels sont les avantages d’Azure Defender pour DNS ?

Azure Defender pour DNS protège les ressources qui sont connectées à Azure DNS contre les problèmes, notamment :

- L’exfiltration de données depuis vos ressources Azure au moyen du tunneling DNS
- La communication entre un programme malveillant et le serveur de commande et contrôle
- La communication avec des domaines malveillants, comme le hameçonnage et l’exploration de données de chiffrement
- Les attaques DNS, par la communication avec des programmes de résolution DNS malveillants 

La liste complète des alertes fournie par Azure Defender pour DNS se trouve sur la [page de référence des alertes](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Les dépendances

Azure Defender pour DNS n’utilise pas d’agent. 

Afin de protéger votre couche DNS, activez Azure Defender pour DNS sur chacun de vos abonnements, comme cela est décrit dans [Activer Azure Defender](enable-azure-defender.md).


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Azure Defender pour DNS. Pour des informations connexes, consultez l’article suivant : 

- Les alertes de sécurité peuvent être générées par Security Center, ou reçues par Security Center depuis d’autres produits de sécurité. Pour exporter toutes ces alertes vers Azure Sentinel, un système SIEM tiers ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers un système SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Activer Azure Defender](enable-azure-defender.md)