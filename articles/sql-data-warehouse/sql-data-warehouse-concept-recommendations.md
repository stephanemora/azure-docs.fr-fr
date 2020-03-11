---
title: Recommandations SQL Analytics
description: Découvrez les recommandations concernant SQL Analytics et la façon dont elles sont générées
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5471236c09737eeef2d4cb7542c245d3087e726c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195955"
---
# <a name="sql-analytics-recommendations"></a>Recommandations SQL Analytics

Cet article décrit les recommandations SQL Analytics énoncées via Azure Advisor.  

SQL Analytics fournit des recommandations pour garantir que la charge de travail de votre entrepôt de données est toujours optimisée pour la performance. Les recommandations sont étroitement intégrées à [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) pour vous fournir les meilleures pratiques directement dans le [Portail Azure](https://aka.ms/Azureadvisor). SQL Analytics collecte la télémétrie et fait émerger des recommandations pour votre charge de travail active sur une cadence quotidienne. Les scénarios de recommandation pris en charge sont décrits ci-dessous, ainsi que le processus d’application des actions recommandées.

Vous pouvez [vérifier vos recommandations](https://aka.ms/Azureadvisor) dès aujourd’hui ! Actuellement, cette fonctionnalité est uniquement applicable aux entrepôts de données Gen2. 

## <a name="data-skew"></a>Asymétrie des données

L’asymétrie des données peut provoquer des déplacements des données ou des goulots d’étranglement de ressource supplémentaires lors de l’exécution de votre charge de travail. La documentation suivante décrit comment identifier l’asymétrie des données et l’empêcher de se produire en sélectionnant une clé de distribution optimale.

- [Identifier et supprimer l’asymétrie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Statistiques absentes ou obsolètes

Des statistiques non optimales peuvent fortement influer sur les performances des requêtes, car l’optimiseur de requête SQL risque de générer des plans de requête non optimaux. La documentation suivante décrit les meilleures pratiques concernant la création et la mise à jour des statistiques :

- [Création et la mise à jour des statistiques sous forme de tableau](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Pour afficher la liste des tableaux concernés par ces suggestions, exécutez le [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) suivant. Azure Advisor exécute en permanence le même script T-SQL pour générer ces suggestions.

## <a name="replicate-tables"></a>Répliquer des tables

Pour suggérer une table répliquée, Advisor détecte des tables candidates en fonction des caractéristiques physiques suivantes :

- Taille de table répliquée
- Nombre de colonnes
- Type de distribution de tables
- Nombre de partitions

Advisor s’appuie en permanence sur une méthode heuristique basée sur la charge de travail, telle que la fréquence d’accès à la table, les lignes renvoyées en moyenne et les seuils relatifs à la taille et à l’activité de l’entrepôt de données, afin de générer des suggestions de haute qualité. 

Vous trouverez ci-dessous une heuristique basée sur la charge de travail que vous pouvez trouver dans le portail Azure pour chaque suggestion de table répliquée :

- Scan avg : indique le pourcentage moyen de lignes retournées à partir de la table pour chaque accès à la table au cours des sept derniers jours.
- Frequent read, no update : indique que la table n’a pas été mise à jour au cours des sept derniers jours d’affichage de l’activité d’accès.
- Read/update ratio : indique le rapport entre la fréquence d’accès à la table et sa mise à jour au cours des sept derniers jours.
- Activity : mesure l’utilisation en fonction de l’activité d’accès. Cela compare l’activité d’accès à la table à l’activité d’accès moyenne à l’entrepôt de données au cours des sept derniers jours. 

Actuellement, Advisor affiche au maximum quatre tables candidates répliquées à la fois, avec des index columnstore en cluster donnant la priorité à l’activité la plus élevée.

> [!IMPORTANT]
> La suggestion de table répliquée n’est pas une preuve complète, et ne prend pas en compte les opérations de déplacement de données. Nous travaillons actuellement sur l’ajout de cette heuristique. En attendant, vous devriez toujours valider votre charge de travail après l’application de la suggestion. Si vous découvrez des suggestions de table répliquées qui entraînent une régression de votre charge de travail, veuillez contacter sqldwadvisor@service.microsoft.com. Pour en savoir plus sur les tables répliquées, voir la [documentation](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table) suivante.
