---
title: Azure Advisor pour PostgreSQL
description: En savoir plus sur les recommandations d’Azure Advisor pour PostgreSQL.
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: a44f808daf32a1175035005067dfbb5edd664ef6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310051"
---
# <a name="azure-advisor-for-postgresql"></a>Azure Advisor pour PostgreSQL
Découvrez la procédure d’application d’Azure Advisor à Azure Database pour PostgreSQL et obtenez des réponses aux questions courantes.
## <a name="what-is-azure-advisor-for-postgresql"></a>Qu’est-ce qu’Azure Advisor pour PostgreSQL ?
Le système Azure Advisor se sert de la télémétrie pour émettre des recommandations en matière de performances et de fiabilité pour votre base de données PostgreSQL. Les recommandations d’Advisor sont réparties dans nos offres de base de données PostgreSQL :
* Azure Database pour PostgreSQL - Serveur unique
* Azure Database pour PostgreSQL - Serveur flexible
* Azure Database pour PostgreSQL - Hyperscale (Citus)

Certaines recommandations sont communes à plusieurs offres de produits, tandis que d’autres sont basées sur des optimisations spécifiques à un produit.
## <a name="where-can-i-view-my-recommendations"></a>Où puis-je voir mes recommandations ?
Les recommandations sont disponibles dans la barre latérale de navigation **Vue d’ensemble** dans le portail Azure. Un préversion s’affiche sous la forme d’une notification de bannière. Les détails peuvent être consultés dans la section **Notifications** située juste en dessous des graphiques d’utilisation des ressources.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Capture d’écran du portail Azure montrant une recommandation d’Azure Advisor.":::

## <a name="recommendation-types"></a>Types de recommandation
Azure Database pour PostgreSQL classe par ordre de priorité les types de recommandations suivants :
* **Performances** : pour améliorer la vitesse de votre serveur PostgreSQL. Cela comprend l’utilisation du processeur, la sollicitation de la mémoire, le regroupement de connexions, l’utilisation du disque et les paramètres de serveur spécifiques au produit. Pour plus d’informations, consultez [Recommandations du conseiller en matière de performances](../advisor/advisor-performance-recommendations.md).
* **Fiabilité** : pour garantir et améliorer la continuité de vos bases de données vitales pour l’entreprise. Cela comprend les limites de stockage, les limites de connexion et les recommandations relatives à la distribution des données hyperscale. Pour plus d’informations, consultez [Recommandations d’Advisor en matière de fiabilité](../advisor/advisor-high-availability-recommendations.md).
* **Coût** : pour optimiser et réduire vos dépenses Azure globales. Cela comprend les recommandations de dimensionnement correct du serveur. Pour plus d’informations, consultez [Recommandations du conseiller en matière de coût](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Compréhension de vos recommandations
* **Planification quotidienne**: pour les bases de données Azure PostgreSQL, nous vérifions la télémétrie du serveur et proposons des recommandations selon une planification quotidienne. Si vous apportez une modification à la configuration de votre serveur, les recommandations existantes restent visibles jusqu’à ce que nous réexaminions la télémétrie le jour suivant. 
* **Historique des performances** : certaines de nos recommandations sont basées sur l’historique des performances. Ces recommandations ne s’affichent qu’une fois que le serveur a utilisé la même configuration pendant 7 jours. Cela nous permet de détecter les modèles d’utilisation intensive (par exemple, une activité élevée du processeur ou un volume de connexion élevé) sur une période prolongée. Si vous approvisionnez un nouveau serveur ou que vous passez à une nouvelle configuration vCore, ces recommandations sont temporairement interrompues. Cela empêche la télémétrie héritée de déclencher des recommandations sur un serveur reconfiguré récemment. Toutefois, cela signifie également que les recommandations basées sur l’historique des performances peuvent ne pas être identifiées immédiatement.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Vue d’ensemble d’Azure Advisor](../advisor/advisor-overview.md).
