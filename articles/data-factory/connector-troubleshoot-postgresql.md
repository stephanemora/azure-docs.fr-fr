---
title: Résoudre les problèmes du connecteur Azure Database for PostgreSQL
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes avec le connecteur Azure Database for PostgreSQL dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8613c6b17007f51caf437fb2d66d2d8e17965007
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390564"
---
# <a name="troubleshoot-the-azure-database-for-postgresql-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Azure Database pour PostgreSQL dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Azure Database for PostgreSQL dans Azure Data Factory et Azure Synapse.

## <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>Code d’erreur : AzurePostgreSqlNpgsqlDataTypeNotSupported

- **Message** : `The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **Recommandation** : Choisissez une colonne de partition avec int, bigint, smallint, serial, bigserial, smallserial, timestamp avec ou sans fuseau horaire, time sans fuseau horaire ni type de données date.

## <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>Code d’erreur : AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **Message** : `Partition column name must be specified.`

- **Cause** : Le nom de colonne de partition n’a pas pu être déterminé automatiquement car il n’a pas été fourni.
 
## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
