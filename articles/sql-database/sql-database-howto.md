---
title: Configurer et gérer
description: Découvrez comment configurer et gérer Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209416"
---
# <a name="how-to-use-azure-sql-database"></a>Utilisation d’Azure SQL Database

Dans cette section, vous trouverez des guides, des scripts et des explications pour vous aider à gérer et à configurer votre base de données Azure SQL Database. Vous trouverez également des guides pratiques spécifiques pour une [base de données unique](sql-database-howto-single-database.md) et une [instance gérée](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Charger les données

- [Copier une base de données unique ou mise en pool dans Azure](sql-database-copy.md)
- [Importer une base de données depuis un fichier BACPAC](sql-database-import.md)
- [Exporter une base de données vers un fichier BACPAC](sql-database-export.md)
- [Chargement des données avec BCP](sql-database-load-from-csv-with-bcp.md)
- [Chargement des données avec ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Synchronisation des données

- [Synchronisation des données SQL](sql-database-sync-data.md)
- [Agent Data Sync](sql-database-data-sync-agent.md)
- [Répliquer les modifications de schéma](sql-database-update-sync-schema.md)
- [Surveiller avec OMS](sql-database-sync-monitor-oms.md)
- [Bonnes pratiques pour Data Sync](sql-database-best-practices-data-sync.md)
- [Dépannage de Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Analyse et réglage

- [Réglage manuel](sql-database-performance-guidance.md)
- [Utiliser des DMV pour surveiller les performances](sql-database-monitoring-with-dmvs.md)
- [Utiliser le Magasin des requêtes pour superviser les performances](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Résoudre les problèmes de performances avec Intelligence Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Utiliser le journal de diagnostic Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Superviser l’espace OLTP en mémoire](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Événements étendus

- [Événements étendus](sql-database-xevent-db-diff-from-svr.md)
- [Stocker des événements étendus dans un fichier d’événements](sql-database-xevent-code-event-file.md)
- [Capturer des événements étendus dans la mémoire tampon](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Configurer des fonctionnalités

- [Configurer l’authentification Azure AD](sql-database-aad-authentication-configure.md)
- [Configurer un accès conditionnel](sql-database-conditional-access.md)
- [Authentification AAD multifacteur](sql-database-ssms-mfa-authentication.md)
- [Configuration de l’authentification multifacteur](sql-database-ssms-mfa-authentication-configure.md)
- [Configurer une stratégie de rétention temporelle](sql-database-temporal-tables-retention-policy.md)
- [Configurer TDE avec BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Effectuer une rotation des clés TDE BYOK](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Supprimer le protecteur TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Configurer OLTP In-Memory](sql-database-in-memory-oltp-migration.md)
- [Configurer Azure Automation](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Développer des applications

- [Connectivité](sql-database-libraries.md)
- [Utiliser Spark Connector](sql-database-spark-connector.md)
- [Authentifier l’application](sql-database-client-id-keys.md)
- [Utiliser le traitement par lot pour améliorer les performances](sql-database-use-batching-to-improve-performance.md)
- [Conseils sur la connectivité](sql-database-connectivity-issues.md)
- [Alias DNS](dns-alias-overview.md)
- [Configurer le PowerShell d’alias DNS](dns-alias-powershell.md)
- [Ports - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C et C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Concevoir des applications

- [Conception de systèmes de récupération d’urgence](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Conception de pools élastiques](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Conception de mises à niveau d’applications](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Conception d’applications SaaS mutualisées

- [Modèles de conception SaaS](saas-tenancy-app-design-patterns.md)
- [Video Indexer SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Sécurité des applications SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Guides pratiques pour les instances gérées](sql-database-howto-managed-instance.md).
- En savoir plus sur les [guides pratiques pour les bases de données uniques](sql-database-howto-single-database.md).
