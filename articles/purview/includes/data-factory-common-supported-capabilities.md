---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 08/10/2021
ms.openlocfilehash: 20a9b85c2d68e1c9b3c33362f393eaec0e255ce7
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122534728"
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
| Stockage Fichier Azure | Oui | 
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

> [!Note]
> La fonctionnalité de traçabilité implique certaines surcharges de performances dans l’activité de copie. Ceux qui configurent des connexions dans Purview constateront peut-être que certaines tâches de copie prennent davantage de temps. Le plus souvent, l’impact est négligeable, voire nul. Veuillez contacter le support en indiquant la comparaison de durée si les travaux de copie prennent beaucoup plus de temps que d’habitude.

#### <a name="known-limitations-on-copy-activity-lineage"></a>Limitations connues de la traçabilité de l’activité Copy

Si vous utilisez les fonctionnalités suivantes de l’activité Copy, la traçabilité n’est pas encore prise en charge :

- Copie de données dans Azure Data Lake Storage Gen1 au format binaire
- Copie de données dans Azure Synapse Analytics avec PolyBase ou l’instruction COPY
- Paramètre de compression pour les fichiers binaires, texte délimité, Excel, JSON et XML
- Options de partition source pour Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server et la table SAP
- Option de détection de partition source pour les magasins basés sur des fichiers
- Copie de données dans un récepteur basé sur des fichiers avec paramètre de nombre maximal de lignes par fichier
- Ajout de colonnes supplémentaires pendant la copie

En plus de la traçabilité des données, le schéma de la ressource de données (affiché sous l’onglet Ressource -> Schéma) est signalé pour les connecteurs suivants :

- Fichiers CSV et Parquet sur Azure Blob,Azure File Storage, ADLS Gen1, ADLS Gen2 et Amazon S3
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata
