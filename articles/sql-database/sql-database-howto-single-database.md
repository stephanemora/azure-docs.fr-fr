---
title: Configurer Azure SQL Database - Singleton | Microsoft Docs
description: Découvrez comment configurer et gérer Azure SQL Database - Single Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: d34853220e423e73c6ca8cf7c76ba616b815b8bd
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439746"
---
# <a name="how-to-use-single-database"></a>Utiliser Single Database

Dans cette section, vous trouverez des guides, des scripts et des explications pour vous aider à gérer et à configurer votre base de données Azure SQL Database - Single Database.

## <a name="migrate"></a>Migrer

- [Migrer vers SQL Database](sql-database-cloud-migrate.md) : découvrez les processus de migration et les outils recommandés pour la migration vers Managed Instance.
- Découvrez comment [gérer une base de données SQL après sa migration](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Configurer des fonctionnalités

- [Configurez la réplication transactionnelle](replication-to-sql-database.md) pour répliquer votre date entre les bases de données.
- [Configurez la détection des menaces](sql-database-threat-detection.md) pour permettre à Azure SQL Database d’identifier les activités suspectes, telles que l’injection de code SQL ou l’accès à partir d’emplacements suspects.
- [Configurez le masquage dynamique des données](sql-database-dynamic-data-masking-get-started-portal.md) pour protéger vos données sensibles.
- [Configurez la conservation des sauvegardes](sql-database-long-term-backup-retention-configure.md) pour une base de données afin de conserver vos sauvegardes sur le Stockage Blob Azure. L’approche [Configurer la rétention des sauvegardes avec Azure Vault (déconseillé)](sql-database-long-term-backup-retention-configure-vault.md) constitue une alternative.
- [Configurez la géoréplication](sql-database-geo-replication-portal.md) pour conserver un réplica de votre base de données dans une autre région.
- [Configurez la sécurité des géo-réplicas](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Superviser et régler votre base de données

- [Activez le réglage automatique](sql-database-automatic-tuning-enable.md) pour permettre à Azure SQL Database d’optimiser les performances de votre charge de travail.
- [Activez les notifications par e-mail pour le réglage automatique](sql-database-automatic-tuning-email-notifications.md) afin d’obtenir des informations sur les recommandations de réglage.
- [Appliquez les recommandations relatives aux performances](sql-database-advisor-portal.md) et optimisez votre base de données.
- [Créez des alertes](sql-database-insights-alerts-portal.md) pour obtenir des notifications à partir d’Azure SQL Database.
- [Résolvez les problèmes de connectivité](sql-database-troubleshoot-common-connection-issues.md) si vous remarquez des problèmes de connectivité entre les applications et la base de données. Vous pouvez aussi utiliser [Resource Health pour les problèmes de connectivité](sql-database-resource-health.md).
- [Gérez l’espace fichier](sql-database-file-space-management.md) pour surveiller l’utilisation du stockage dans votre base de données.

## <a name="query-distributed-data"></a>Interroger des données distribuées

- [Interrogez des données partitionnées verticalement](sql-database-elastic-query-getting-started-vertical.md) entre plusieurs bases de données.
- [Créez des rapports sur des couches de données Scale-out](sql-database-elastic-query-horizontal-partitioning.md).
- [Interrogez des tables avec des schémas différents](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Tâches de base de données élastique

- [Créez et gérez](elastic-jobs-powershell.md) des tâches de bases de données élastiques à l'aide de PowerShell.
- [Créez et gérez](elastic-jobs-tsql.md) des tâches de bases de données élastiques à l'aide de Transact-SQL.
- [Migrez à partir d’anciens travaux élastiques](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Partitionnement de base de données

- [Mettez à niveau la bibliothèque cliente de base de données élastique](sql-database-elastic-scale-upgrade-client-library.md).
- [Créez une application partitionnée](sql-database-elastic-scale-get-started.md).
- [Interrogez des données partitionnées horizontalement](sql-database-elastic-query-getting-started.md).
- Exécutez des [requêtes sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md).
- [Déplacez des données partitionnées](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Configurez la sécurité](sql-database-elastic-scale-split-merge-security-configuration.md) dans des partitions de base de données.
- [Ajoutez une partition](sql-database-elastic-scale-add-a-shard.md) à l’ensemble actuel de partitions de base de données.
- [Résolvez les problèmes de carte de partitions](sql-database-elastic-database-recovery-manager.md).
- [Migrez la base de données partitionnée](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Créez des compteurs](sql-database-elastic-database-perf-counters.md).
- [Utilisez le framework d’entité](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) pour interroger des données partitionnées.
- [Utilisez le framework Dapper](sql-database-elastic-scale-working-with-dapper.md) pour interroger des données partitionnées.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [Guides pratiques dans Managed Instance](sql-database-howto-managed-instance.md)
