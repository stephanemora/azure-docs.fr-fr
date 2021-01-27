---
title: Azure Defender pour DNS – Avantages et fonctionnalités
description: Découvrir les avantages et les fonctionnalités d’Azure Defender pour DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 957e39f7629337182c3e19a1a514c42883666301
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796997"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Présentation d’Azure Defender pour DNS

[Azure DNS](../dns/dns-overview.md) est un service d’hébergement pour les domaines DNS, qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS à l’aide des mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure.

Azure Defender pour DNS fournit une couche supplémentaire de protection pour vos ressources cloud par :

- la supervision en continu de toutes les requêtes DNS émanant de vos ressources Azure
- l’exécution de l’analytique de sécurité avancée pour vous avertir des activités suspectes

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|PRÉVERSION<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prix :|**Azure Defender pour DNS** est facturé, comme stipulé à [la page des tarifs](security-center-pricing.md).|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Non](./media/icons/no-icon.png) National/souverain (US Gov, Chine Gov, autres Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Quels sont les avantages d’Azure Defender pour DNS ?

Azure Defender pour DNS protège contre certrains problèmes, notamment :

- L’exfiltration de données depuis vos ressources Azure au moyen du tunneling DNS
- La communication entre un programme malveillant et le serveur de commande et contrôle
- La communication avec des domaines malveillants, comme le hameçonnage et l’exploration de données de chiffrement
- Les attaques DNS, par la communication avec des programmes de résolution DNS malveillants 

La liste complète des alertes fournie par Azure Defender pour DNS se trouve sur la [page de référence des alertes](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Les dépendances

Azure Defender pour DNS n’utilise pas d’agent. 

Afin de protéger votre couche DNS, activez Azure Defender pour DNS sur chacun de vos abonnements, comme cela est décrit dans [Activer Azure Defender](security-center-pricing.md#enable-azure-defender).


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Azure Defender pour DNS. Pour des informations connexes, consultez l’article suivant : 

- Les alertes de sécurité peuvent être générées par Security Center, ou reçues par Security Center depuis d’autres produits de sécurité. Pour exporter toutes ces alertes vers Azure Sentinel, un système SIEM tiers ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers un système SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Activer Azure Defender](security-center-pricing.md#enable-azure-defender)