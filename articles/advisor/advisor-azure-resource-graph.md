---
title: Données Advisor dans Azure Resource Graph
description: Exécuter des requêtes sur les données Advisor dans Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: c0786d1d09ff61ddd9c375c68b7199521e319a4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85117832"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Interroger les données Advisor dans l’Explorateur Resource Graph (Azure Resource Graph)

Les ressources Advisor sont désormais intégrées à [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Cela constitue une base pour un grand nombre de scénarios clients de grande échelle concernant les recommandations Advisor. Les quelques scénarios qui n’étaient pas possibles à grande échelle et qui peuvent désormais être réalisés avec Resource Graph sont les suivants :
* La possibilité d’exécuter des requêtes complexes sur l’ensemble des abonnements dans le portail Azure
* Le résumé des recommandations par catégorie (fiabilité, performances, etc.) et par impact (élevé, moyen, faible)
* L’obtention de toutes les recommandations d’un type particulier
* Le nombre de ressources impactées par catégorie de recommandation

![Advisor dans l’Explorateur Azure Resource Graph](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Types de ressources Advisor dans Azure Graph

Types de ressources Advisor disponibles dans [Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/) : Trois types de ressources sont disponibles pour l’interrogation des ressources Advisor. Voici la liste des ressources qui peuvent désormais être interrogées dans Resource Graph.
* Microsoft.Advisor/configurations
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/suppressions

Ces types de ressources sont listés dans une nouvelle table nommée AdvisorResources, que vous pouvez également interroger dans l’Explorateur Resource Graph sur le portail Azure.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les recommandations d’Advisor, consultez :
* [Présentation du conseiller Azure](advisor-overview.md)
* [Prise en main d’Advisor](advisor-get-started.md)
* [Recommandations d’Advisor en matière de coûts](advisor-cost-recommendations.md)
* [Recommandations d’Advisor en matière de fiabilité](advisor-high-availability-recommendations.md)
* [Recommandations d’Advisor en matière de performances](advisor-performance-recommendations.md)
* [Recommandations d’Advisor en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations d’Advisor en matière d’excellence opérationnelle](advisor-operational-excellence-recommendations.md)
* [API REST Advisor](https://docs.microsoft.com/rest/api/advisor/)
