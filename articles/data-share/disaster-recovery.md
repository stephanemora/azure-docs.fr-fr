---
title: Récupération d’urgence pour Azure Data Share
description: Récupération d’urgence pour Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75475967"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Récupération d’urgence pour Azure Data Share

Cet article explique comment configurer un environnement de récupération d’urgence pour Azure Data Share. Les pannes de centres de données Azure sont rares, mais elles peuvent durer de quelques minutes à plusieurs heures. Les pannes de centre de données peuvent entraîner des perturbations dans les environnements qui reposent sur le partage des données par le fournisseur de données. En suivant les étapes décrites dans cet article, les fournisseurs de données peuvent continuer à partager des données avec les consommateurs de données en cas de panne d’un centre de données pour la région primaire qui héberge le partage de données. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Atteindre la continuité d’activité pour Azure Data Share

Pour se préparer en cas de panne d’un centre de données, le fournisseur de données peut disposer d’un environnement de partage de données provisionné dans une région secondaire. Des mesures peuvent être prises pour garantir un basculement sans heurts dans cette situation. 

Les fournisseurs de données ont la possibilité de provisionner des ressources Azure Data Share secondaires dans une région supplémentaire. Ces ressources Data Share peuvent être configurées de façon à inclure des jeux de données qui existent dans l’environnement de partage de données primaire. Les consommateurs de données peuvent être ajoutés au partage de données lors de la configuration de l’environnement de récupération d’urgence ou ajoutés ultérieurement (par exemple, lors d’une procédure de basculement manuel).

Si les consommateurs de données disposent d’un abonnement de partage actif dans un environnement secondaire provisionné pour la récupération d’urgence, ils peuvent activer la planification d’instantanés dans le cadre d’un basculement. S’ils ne veulent pas s’abonner à une région secondaire à des fins de récupération d’urgence, ils peuvent être invités ultérieurement dans le partage de données secondaire. 

Les consommateurs de données peuvent avoir un abonnement de partage actif qui soit inactif en matière de récupération d’urgence ; sinon, les fournisseurs de données peuvent les ajouter ultérieurement dans le cadre de procédures de basculement manuel. 

## <a name="related-information"></a>Informations connexes

- [Continuité d’activité et reprise d’activité](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Créer une haute disponibilité dans une stratégie BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).




