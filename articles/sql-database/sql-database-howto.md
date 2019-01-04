---
title: Configurer Azure SQL Database | Microsoft Docs
description: Découvrez comment configurer et gérer Azure SQL Database.
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
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439536"
---
# <a name="how-to-use-azure-sql-database"></a>Utilisation d’Azure SQL Database

Dans cette section, vous trouverez des guides, des scripts et des explications pour vous aider à gérer et à configurer votre base de données Azure SQL Database. Vous trouverez également des guides pratiques spécifiques pour une [base de données unique](sql-database-howto-single-database.md) et une [instance gérée](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Charger des données

- [Copier une base de données unique dans Azure](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Importer une base de données depuis un fichier BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Exporter une base de données vers un fichier BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Chargement des données avec BCP](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Chargement des données avec ADF](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Synchronisation des données

- [Synchronisation des données SQL](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Agent Data Sync](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Répliquer les modifications de schéma](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Surveiller avec OMS](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Bonnes pratiques pour Data Sync](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Dépannage de Data Sync](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Analyse et réglage

-  [Réglage manuel](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Utiliser des DMV pour surveiller les performances](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Utiliser le Magasin des requêtes pour superviser les performances](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Résoudre les problèmes de performances avec Intelligence Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Utiliser le journal de diagnostic Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Superviser l’espace OLTP en mémoire](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Événements étendus

- [Événements étendus](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Stocker des événements étendus dans un fichier d’événements](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Capturer des événements étendus dans la mémoire tampon](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Configurer des fonctionnalités

- [Configurer l’authentification Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Configurer un accès conditionnel](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Authentification AAD multifacteur](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Configuration de l’authentification multifacteur](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Configurer une stratégie de rétention temporelle](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [Configurer TDE avec BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [Effectuer une rotation des clés TDE BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Supprimer le protecteur TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Configurer OLTP In-Memory](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Configurer Azure Automation](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Développer des applications

- [Connectivité](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Utiliser Spark Connector](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Authentifier l’application](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Messages d’erreur](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [Utiliser le traitement par lot pour améliorer les performances](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Conseils sur la connectivité](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [Alias DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [Configurer le PowerShell d’alias DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Ports - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C et C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Concevoir des applications

- [Conception de systèmes de récupération d’urgence](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Conception de pools élastiques](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Conception de mises à niveau d’applications](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Conception d’applications SaaS mutualisées

- [Modèles de conception SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [Video Indexer SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [Sécurité des applications SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [guides pratiques dans une instance gérée](sql-database-howto-managed-instance.md).
- En savoir plus sur les [guides de procédures dans la base de données unique](sql-database-howto-single-database.md).
