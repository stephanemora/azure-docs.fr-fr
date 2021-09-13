---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 08/25/2021
ms.openlocfilehash: 61f0dc4bf556c992721b5cd7cd2859ef30d8cbb9
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123355380"
---
### <a name="copy-activity-support"></a>Prise en charge de l’activité de copie

| Banque de données | Prise en charge | 
| ------------------- | ------------------- | 
| Stockage Blob Azure | Oui |
| Recherche cognitive Azure | Oui | 
| Azure Cosmos DB\* (API SQL) | Oui | 
| API d’Azure Cosmos DB pour MongoDB\* | Oui |
| Azure Data Explorer\* | Oui | 
| Azure Data Lake Storage Gen1 | Oui | 
| Azure Data Lake Storage Gen2 | Oui | 
| Azure Database for Maria DB \* | Oui | 
| Azure Database pour MySQL\* | Oui | 
| Azure Database pour PostgreSQL \* | Oui |
| Azure Files | Oui | 
| Azure SQL Database \* | Oui | 
| Azure SQL Managed Instance\* | Oui | 
| Azure Synapse Analytics\* | Oui | 
| Stockage de table Azure | Oui |
| Amazon S3 | Oui | 
| Hive\* | Oui | 
| Tableau SAP *(lors de la connexion à SAP ECC ou SAP S/4HANA)* | Oui |
| SQL Server \* | Oui | 
| Teradata\* | Oui |

*\* Azure Purview ne prend pas en charge à l’heure actuelle les requêtes ni les procédures stockées pour la traçabilité et l’analyse. La traçabilité est limitée aux sources de table et de vue uniquement.*

#### <a name="known-limitations-on-copy-activity-lineage"></a>Limitations connues de la traçabilité de l’activité Copy

Si vous utilisez les fonctionnalités suivantes de l’activité Copy, la traçabilité n’est pas encore prise en charge :

- Copie de données dans Azure Data Lake Storage Gen1 au format binaire
- Copie de données dans Azure Synapse Analytics avec PolyBase ou l’instruction COPY
- Paramètre de compression pour les fichiers binaires, texte délimité, Excel, JSON et XML
- Options de partition source pour Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server et la table SAP
- Option de détection de partition source pour les magasins basés sur des fichiers
- Copie de données dans un récepteur basé sur des fichiers avec paramètre de nombre maximal de lignes par fichier

En plus de la traçabilité des données, le schéma de la ressource de données (affiché sous l’onglet Ressource -> Schéma) est signalé pour les connecteurs suivants :

- Fichiers CSV et Parquet sur Azure Blob, Azure Files, ADLS Gen1, ADLS Gen2 et Amazon S3
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata

### <a name="data-flow-support"></a>Prise en charge de flux de données

| Banque de données | Prise en charge |
| ------------------- | ------------------- | 
| Stockage Blob Azure | Oui |
| Azure Cosmos DB\* (API SQL) | Oui | 
| Azure Data Lake Storage Gen1 | Oui |
| Azure Data Lake Storage Gen2 | Oui |
| Azure Database pour MySQL\* | Oui | 
| Azure Database pour PostgreSQL \* | Oui |
| Azure SQL Database \* | Oui |
| Azure SQL Managed Instance\* | Oui | 
| Azure Synapse Analytics\* | Oui |

*\* Azure Purview ne prend pas en charge à l’heure actuelle les requêtes ni les procédures stockées pour la traçabilité et l’analyse. La traçabilité est limitée aux sources de table et de vue uniquement.*