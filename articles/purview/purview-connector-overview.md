---
title: Vue d’ensemble du connecteur Purview
description: Cet article décrit les différentes fonctionnalités et banques de données prises en charge dans Purview
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 58b8ef4959c54e8c597e3f54864caae77dfd1fa8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061292"
---
# <a name="supported-data-stores"></a>Magasins de données pris en charge

Purview prend en charge les banques de données suivantes. Sélectionnez chaque banque de données pour découvrir le détail des fonctionnalités prises en charge et des configurations correspondantes.

## <a name="purview-data-sources"></a>Sources de données Purview

|**Catégorie**|  **Banque de données**  |**Extraction des métadonnées**|**Analyse complète**|**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|---|---|
| Azure | [Stockage Blob Azure](register-scan-azure-blob-storage-source.md)| [Oui](register-scan-azure-blob-storage-source.md#register) | [Oui](register-scan-azure-blob-storage-source.md#scan)|[Oui](register-scan-azure-blob-storage-source.md#scan) | [Oui](register-scan-azure-blob-storage-source.md#scan)|[Oui](register-scan-azure-blob-storage-source.md#scan)| Oui | Non|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [Oui](register-scan-azure-cosmos-database.md#register) | [Oui](register-scan-azure-cosmos-database.md#scan)|[Oui](register-scan-azure-cosmos-database.md#scan) | [Oui](register-scan-azure-cosmos-database.md#scan)|[Oui](register-scan-azure-cosmos-database.md#scan)|Non|Non|
||[Explorateur de données Azure](register-scan-azure-data-explorer.md)| [Oui](register-scan-azure-data-explorer.md#register) | [Oui](register-scan-azure-data-explorer.md#scan) | [Oui](register-scan-azure-data-explorer.md#scan) | [Oui](register-scan-azure-data-explorer.md#scan)| [Oui](register-scan-azure-data-explorer.md#scan)| Non | Non |
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [Oui](register-scan-adls-gen1.md#register) | [Oui](register-scan-adls-gen1.md#scan)|[Oui](register-scan-adls-gen1.md#scan) | [Oui](register-scan-adls-gen1.md#scan)|[Oui](register-scan-adls-gen1.md#scan)| Non |[Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [Oui](register-scan-adls-gen2.md#register) | [Oui](register-scan-adls-gen2.md#scan)|[Oui](register-scan-adls-gen2.md#scan) | [Oui](register-scan-adls-gen2.md#scan)|[Oui](register-scan-adls-gen2.md#scan)| Oui | [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |
||[Pool SQL dédié Azure (anciennement SQL DW)](register-scan-azure-synapse-analytics.md)| [Oui](register-scan-azure-synapse-analytics.md#register) | [Oui](register-scan-azure-synapse-analytics.md#scan)| [Oui](register-scan-azure-synapse-analytics.md#scan)| [Oui](register-scan-azure-synapse-analytics.md#scan)| [Oui](register-scan-azure-synapse-analytics.md#scan)| Non | Non|
||[Azure Files](register-scan-azure-files-storage-source.md)|[Oui](register-scan-azure-files-storage-source.md#register) | [Oui](register-scan-azure-files-storage-source.md#scan) | [Oui](register-scan-azure-files-storage-source.md#scan) | [Oui](register-scan-azure-files-storage-source.md#scan) | [Oui](register-scan-azure-files-storage-source.md#scan) | Non | Non |
||[Azure SQL Database](register-scan-azure-sql-database.md)| [Oui](register-scan-azure-sql-database.md#register) | [Oui](register-scan-azure-sql-database.md#scan)|[Oui](register-scan-azure-sql-database.md#scan) | [Oui](register-scan-azure-sql-database.md#scan)|[Oui](register-scan-azure-sql-database.md#scan)| Non |[Traçabilité des données Data Factory](how-to-link-azure-data-factory.md)|
||[Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)| [Oui](register-scan-azure-sql-database-managed-instance.md#register) | [Oui](register-scan-azure-sql-database-managed-instance.md#scan)| [Oui](register-scan-azure-sql-database-managed-instance.md#scan) | [Oui](register-scan-azure-sql-database-managed-instance.md#scan) | [Oui](register-scan-azure-sql-database-managed-instance.md#scan) | Non | [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |
||[Azure Synapse Analytics (Espace de travail)](register-scan-synapse-workspace.md)| [Oui](register-scan-synapse-workspace.md#register) | [Oui](register-scan-synapse-workspace.md#scan)| [Oui](register-scan-synapse-workspace.md#scan) | [Oui](register-scan-synapse-workspace.md#scan)| [Oui](register-scan-synapse-workspace.md#scan)| Non| [Oui](how-to-lineage-azure-synapse-analytics.md)|
||[Azure Database pour MySQL](register-scan-azure-mysql-database.md)| [Oui](register-scan-azure-mysql-database.md#register) | [Oui](register-scan-azure-mysql-database.md#scan)| [Oui*](register-scan-azure-mysql-database.md#scan) | [Oui](register-scan-azure-mysql-database.md#scan) | [Oui](register-scan-azure-mysql-database.md#scan) | Non | [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |
||[Base de données Azure pour PostgreSQL](register-scan-azure-postgresql.md)| [Oui](register-scan-azure-postgresql.md#register) | [Oui](register-scan-azure-postgresql.md#scan)| [Oui](register-scan-azure-postgresql.md#scan) | [Oui](register-scan-azure-postgresql.md#scan) | [Oui](register-scan-azure-postgresql.md#scan) | Non | [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |
|Base de données|[Cassandra](register-scan-cassandra-source.md)|[Oui](register-scan-cassandra-source.md#register) | [Oui](register-scan-cassandra-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-cassandra.md)|
||[Google BigQuery](register-scan-google-bigquery-source.md)| [Oui](register-scan-google-bigquery-source.md#register)| [Oui](register-scan-google-bigquery-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-google-bigquery.md)|
||[Hive Metastore DB](register-scan-hive-metastore-source.md)| [Oui](register-scan-hive-metastore-source.md#register)| [Oui](register-scan-hive-metastore-source.md#scan)| Non | Non | Non | Non| Oui |
||[Oracle DB](register-scan-oracle-source.md)| [Oui](register-scan-oracle-source.md#register)| [Oui](register-scan-oracle-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-oracle.md)|
||[SQL Server](register-scan-on-premises-sql-server.md)| [Oui](register-scan-on-premises-sql-server.md#register) | [Oui](register-scan-on-premises-sql-server.md#scan) | [Oui](register-scan-on-premises-sql-server.md#scan) | [Oui](register-scan-on-premises-sql-server.md#scan) | [Oui](register-scan-on-premises-sql-server.md#scan) | Non| [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |
||[Teradata](register-scan-teradata-source.md)| [Oui](register-scan-teradata-source.md#register)| [Oui](register-scan-teradata-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-teradata.md)|
|Fichier|[Amazon S3](register-scan-amazon-s3.md)|[Oui](register-scan-amazon-s3.md)| [Oui](register-scan-amazon-s3.md)| [Oui](register-scan-amazon-s3.md)| [Oui](register-scan-amazon-s3.md)| [Oui](register-scan-amazon-s3.md)| Non| Oui|
|Services et applications|[Erwin](register-scan-erwin-source.md)| [Oui](register-scan-erwin-source.md#register)| [Oui](register-scan-erwin-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-erwin.md)|
||[Looker](register-scan-looker-source.md)| [Oui](register-scan-looker-source.md#register)| [Oui](register-scan-looker-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-looker.md)|
||[Power BI](register-scan-power-bi-tenant.md)| [Oui](register-scan-power-bi-tenant.md#register)| [Oui](register-scan-power-bi-tenant.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-powerbi.md)|
||[SAP ECC](register-scan-sapecc-source.md)| [Oui](register-scan-sapecc-source.md#register)| [Oui](register-scan-sapecc-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-sapecc.md)|
||[SAP S4HANA](register-scan-saps4hana-source.md)| [Oui](register-scan-saps4hana-source.md#register)| [Oui](register-scan-saps4hana-source.md#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-sapecc.md)|

\*Purview s’appuie sur les métadonnées UPDATE_TIME de Azure Database pour MySQL pour les analyses incrémentielles. Dans certains cas, ce champ peut ne pas persister dans la base de données et une analyse complète est effectuée. Pour plus d’informations, consultez [Le tableau INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) pour MySQL. 

> [!NOTE]
> Actuellement, Purview ne peut pas analyser une ressource contenant `/`, `\` ou `#` dans son nom. Pour étendre votre analyse et éviter les ressources de scan qui contiennent ces caractères dans le nom de la ressource, utilisez l’exemple dans [Enregistrer et scanner une base de données Azure SQL Database](register-scan-azure-sql-database.md#creating-the-scan).

\*Purview s’appuie sur les métadonnées UPDATE_TIME de Azure Database pour MySQL pour les analyses incrémentielles. Dans certains cas, ce champ peut ne pas persister dans la base de données et une analyse complète est effectuée. Pour plus d’informations, consultez [Le tableau INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) pour MySQL. 

> [!NOTE]
> Actuellement, Purview ne peut pas analyser une ressource contenant `/`, `\` ou `#` dans son nom. Pour étendre votre analyse et éviter les ressources de scan qui contiennent ces caractères dans le nom de la ressource, utilisez l’exemple dans [Enregistrer et scanner une base de données Azure SQL Database](register-scan-azure-sql-database.md#creating-the-scan).

## <a name="scan-regions"></a>Analyser des régions
Voici une liste de toutes les régions de la source de données Azure (centre de données) sur lesquelles l’analyseur Purview s’exécute. Si votre source de données Azure se trouve dans une région qui ne figure pas dans cette liste, l’analyseur s’exécutera dans la région de votre instance de Purview.
 
### <a name="purview-scanner-regions"></a>Régions de l’analyseur Purview

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- AsieSudEst
- WestEurope
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JaponEst
- SouthAfricaNorth
- FranceCentral
- KoreaCentral
- CentralUS
- NorthCentralUS
- EastAsia
- USACentreOuest
- AustralieSudEst

## <a name="next-steps"></a>Étapes suivantes

- [Inscrire et analyser une source Stockage Blob Azure](register-scan-azure-blob-storage-source.md)
