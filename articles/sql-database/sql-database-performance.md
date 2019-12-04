---
title: Surveiller et améliorer les performances
description: Azure SQL Database fournit des outils de performances pour vous aider à identifier les zones susceptibles d’améliorer les performances actuelles des requêtes.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 474c2f4f00374ce785b81fe048e11cb353b3078a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151220"
---
# <a name="monitor-and-improve-performance"></a>Surveiller et améliorer les performances

Azure SQL Database identifie les problèmes potentiels de votre base de données et recommande les actions susceptibles d’améliorer les performances de votre charge de travail en fournissant des recommandations et des actions de réglage intelligent.

## <a name="performance-tuning-options"></a>Options de réglage des performances

Les options de réglage des performances disponibles Azure SQL Database sont disponibles dans le menu de navigation de la base de données sous « Performances intelligentes » :

| Option de réglage des performances | Prise en charge d’une base de données unique et d’une base de données mise en pool | Prise en charge de la base de données d’instance |
| :----------------------------- | ----- | ----- |
| **Vue d’ensemble des performances** : Surveillez toutes les activités de performances de votre base de données. | OUI | Non | 
| **Recommandations relatives aux performances** : Affiche des recommandations en matière de performances pouvant améliorer les performances de votre charge de travail. | OUI | Non | 
| **Query Performance Insight** : Affiche les performances des requêtes les plus gourmandes sur la base de données. | OUI | Non | 
| **Réglage automatique** : Utilisez Azure SQL Database pour optimiser automatiquement le niveau de performance de votre base de données. | OUI | Non | 

## <a name="performance-overview"></a>Vue d’ensemble sur les performances

Cette vue fournit un résumé des performances de votre base de données et vous aide à optimiser les performances et à résoudre les problèmes. 

![Vue d’ensemble des performances pour Azure SQL Database](./media/sql-database-performance/performance-overview-annotated.png)

* La vignette **Recommandations** fournit une répartition des recommandations de paramétrage de votre base de données (les trois premières recommandations sont affichées s’il en existe plus). La page **[Recommandations sur les performances](#performance-recommendations)** s’affiche si vous cliquez sur cette vignette. 
* La vignette **Activité de paramétrage** fournit un résumé des actions de paramétrage en cours et terminées pour votre base de données, ce qui vous donne un aperçu rapide de l’historique des activités de paramétrage. Cliquez sur cette vignette pour afficher l'historique de paramétrage complet de votre base de données.
* La vignette **Paramétrage automatique** affiche la [configuration de paramétrage automatique](sql-database-automatic-tuning-enable.md) de votre base de données (options de paramétrage qui sont automatiquement appliquées à votre base de données). Cliquez sur cette vignette pour ouvrir la boîte de dialogue de configuration de l'automatisation.
* La vignette **Requêtes de base de données** affiche le résumé des performances de requête de votre base de données (utilisation DTU globale et requêtes les plus consommatrices de ressources). La page **[Analyse des performances des requêtes](#query-performance-insight)** s’affiche si vous cliquez sur cette vignette.

## <a name="performance-recommendations"></a>Recommandations en matière de performances

Cette page fournit des [recommandations de réglage intelligent](sql-database-advisor.md) qui peuvent améliorer les performances de votre base de données. Les types de recommandations suivants sont affichés sur cette page :

* Recommandations sur les index à créer ou à supprimer.
* Recommandations en cas de problèmes de schéma dans la base de données.
* Recommandations quand les requêtes peuvent tirer parti des requêtes paramétrables.

![Recommandations relatives aux performances pour Azure SQL Database](./media/sql-database-performance/performance-recommendations-annotated.png)

Vous trouverez également un historique complet des actions de réglage qui ont été appliquées dans le passé.

Découvrez comment rechercher et appliquer les recommandations relatives aux performances dans l’article [Rechercher et appliquer les recommandations en matière de performances](sql-database-advisor-portal.md).

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) vous permet de passer moins de temps à résoudre les problèmes de performances des bases de données en fournissant :

* Une meilleure compréhension de la consommation des ressources des bases de données (DTU). 
* Les requêtes principales consommatrices d’UC peuvent être réglées pour améliorer les performances. 
* La possibilité d’examiner les détails d’une requête. 

  ![Query Performance Insight pour Azure SQL Database](./media/sql-database-performance/query-performance-insights-annotated.png)

Pour plus d’informations sur cette page, voir l’article **[How to use Query Performance Insight (Utilisation de Query Performance Insight)](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Réglage automatique

Azure SQL Database peut régler automatiquement les performances des bases de données en appliquant les [recommandations relatives aux performances](sql-database-advisor.md). Pour l’activer, lire [How to enable automatic tuning (Activation du réglage automatique)](sql-database-automatic-tuning-enable.md).

  ![Réglage automatique pour Azure SQL Database](./media/sql-database-performance/automatic-tuning-annotated.png)

Pour en savoir plus, voir l’article [Automatic tuning (Réglage automatique)](sql-database-automatic-tuning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Guide des performances Azure SQL Database pour les bases de données uniques](sql-database-performance-guidance.md)
* [Quand utiliser un pool élastique ?](sql-database-elastic-pool-guidance.md)
