---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 8c4baccdbfb0f6162e666d6901e0080202a3b7e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212332"
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
| Oracle \* | Oui |
| Tableau SAP *(lors de la connexion à SAP ECC ou SAP S/4HANA)* | Oui |
| SQL Server \* | Oui | 
| Teradata\* | Oui |

*\* Azure Purview ne prend pas en charge à l’heure actuelle les requêtes ni les procédures stockées pour la traçabilité et l’analyse. La traçabilité est limitée aux sources de table et de vue uniquement.*

Si vous utilisez le runtime d’intégration autohébergé, notez la version minimale avec prise en charge de la traçabilité pour les actions suivantes :
- Copie de données à partir d’Oracle : version 5.10 ou ultérieure
- Copie de données dans Azure Synapse Analytics via la commande COPY ou PolyBase : version 5.10 ou ultérieure

#### <a name="limitations-on-copy-activity-lineage"></a>Limitations relatives à la traçabilité de l’activité Copy

Si vous utilisez les fonctionnalités suivantes de l’activité Copy, la traçabilité n’est pas encore prise en charge :

- Copie de données dans Azure Data Lake Storage Gen1 au format binaire
- Paramètre de compression pour les fichiers binaires, texte délimité, Excel, JSON et XML
- Options de partition source pour Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server et la table SAP
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

#### <a name="limitations-on-data-flow-lineage"></a>Limitations relatives à la traçabilité du flux de données

Pour le moment, la traçabilité du flux de données ne s’intègre pas à l’[ensemble de ressources](../concept-resource-sets.md) de Purview.
