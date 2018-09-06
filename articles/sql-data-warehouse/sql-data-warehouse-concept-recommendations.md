---
title: Suggestions concernant SQL Data Warehouse - Concepts | Microsoft Docs
description: Découvrez les suggestions concernant SQL Data Warehouse et la façon dont elles sont générées
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 35ae3d1a8a6de2d348f90e2f55b732421b879917
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307635"
---
# <a name="sql-data-warehouse-recommendations"></a>Suggestions concernant SQL Data Warehouse

Cet article décrit les suggestions énoncées par SQL Data Warehouse via Azure Advisor.  

SQL Data Warehouse fournit des suggestions pour assurer que votre entrepôt de données est toujours optimisé pour la performance. Les suggestions de l’entrepôt de données sont étroitement intégrées à [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) pour vous fournir les bonnes pratiques directement dans [le portail Azure](https://aka.ms/Azureadvisor). SQL Data Warehouse analyse l’état actuel de votre entrepôt de données et collecte des données de télémétrie ainsi que des suggestions de surfaces pour votre charge de travail active, tous les jours. Les scénarios de suggestion de l’entrepôt de données pris en charge sont décrits ci-dessous, ainsi que le processus d’application des actions recommandées.

Si vous avez des commentaires sur SQL Data Warehouse Advisor ou que vous rencontrez des problèmes, veuillez contacter [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Cliquez sur [ici](https://aka.ms/Azureadvisor) pour vérifier vos suggestions dès aujourd'hui ! Actuellement, cette fonctionnalité est uniquement applicable aux entrepôts de données Gen2. 

## <a name="data-skew"></a>Asymétrie des données

L’asymétrie des données peut provoquer des déplacements des données ou des goulots d’étranglement de ressource supplémentaires lors de l’exécution de votre charge de travail. La documentation suivante décrit comment identifier l’asymétrie des données et l’empêcher de se produire en sélectionnant une clé de distribution optimale.

- [Identifier et supprimer l’asymétrie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Statistiques absentes ou obsolètes

Des statistiques non optimales peuvent fortement influer sur les performances des requêtes, car l’optimiseur de requête du SQL Data Warehouse risque de générer des plans de requête non optimaux. La documentation suivante décrit les meilleures pratiques concernant la création et la mise à jour des statistiques :

- [Création et la mise à jour des statistiques sous forme de tableau](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistic)

Pour ces deux suggestions, l’Assistant s’exécute en continu le [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) suivant pour identifier les tables affectées par l’asymétrie et les recommandations de statistiques.
