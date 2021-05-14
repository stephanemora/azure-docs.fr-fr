---
title: Vue d’ensemble du connecteur Purview
description: Cet article décrit les différentes fonctionnalités et banques de données prises en charge dans Purview
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 66ed42b79e4f6b5bac7fcb76b95a04257a437daf
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109733151"
---
# <a name="supported-data-stores"></a>Magasins de données pris en charge

Purview prend en charge les banques de données suivantes. Cliquez sur chaque banque de données pour découvrir les fonctionnalités prises en charge et les configurations correspondantes en détail.

## <a name="purview-data-sources"></a>Sources de données Purview

|**Catégorie**|  **Banque de données**  |**Extraction des métadonnées**|**Analyse complète**|**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Traçabilité**|
|---|---|---|---|---|---|---|---|
| Azure | [Stockage Blob Azure](register-scan-azure-blob-storage-source.md)| Oui| Oui| Oui| Oui| Oui| Oui|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Oui| Oui| Oui| Oui| Oui| Oui|
||[Explorateur de données Azure](register-scan-azure-data-explorer.md)|Oui| Oui| Oui| Oui| Oui| Oui|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Oui| Oui| Oui| Oui| Oui| Oui|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Oui| Oui| Oui| Oui| Oui| Oui|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Oui| Oui| Non| Oui| Oui| Oui|
||[Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)|Oui| Oui| Non| Oui| Oui| Oui|
||[Azure Synapse Analytics (anciennement SQL DW)](register-scan-azure-synapse-analytics.md)|Oui| Oui| Non| Oui| Oui| Oui|
|Base de données|[Oracle DB](register-scan-oracle-source.md)|Oui| Oui| Non| Non| Non| Oui|
||[SQL Server](register-scan-on-premises-sql-server.md)|Oui| Oui| Non| Oui| Oui| Oui|
||[Teradata](register-scan-teradata-source.md)|Oui| Oui| Non| Non| Non| Oui|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Oui| Oui| Non| Non| Non| Oui|
|Services et applications|[SAP ECC](register-scan-sapecc-source.md)|Oui| Oui| Non| Oui| Oui| Oui|
||[SAP S4HANA](register-scan-saps4hana-source.md)|Oui| Oui| Non| Oui| Oui| Oui|

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