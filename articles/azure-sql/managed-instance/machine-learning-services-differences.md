---
title: Différences clés pour Machine Learning Services
description: Cet article décrit les principales différences entre la version Azure SQL Managed Instance et la version SQL Server de Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599542"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Principales différences entre la version Azure SQL Managed Instance et la version SQL Server de Machine Learning Services

Cet article décrit les quelques différences clés de fonctionnalités entre [Machine Learning Services dans Azure SQL Managed Instance](machine-learning-services-overview.md) et [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning).

## <a name="language-support"></a>Support multilingue

Machine Learning Services dans SQL Managed Instance et SQL Server prennent en charge l’[infrastructure d’extensibilité](/sql/machine-learning/concepts/extensibility-framework) Python et R. Les différences clés dans SQL Managed Instance sont les suivantes :

- Seuls Python et R sont pris en charge. Les langages externes tels que Java ne peuvent pas être ajoutés.

- Les versions initiales de Python et de R sont différentes.

  | Plateforme                   | Version du runtime Python           | Versions du runtime R                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL Managed Instance | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 et 3.7.2 (CU22 et versions ultérieures) | 3.3.3 et 3.5.2 (CU22 et versions ultérieures)     |
  | SQL Server 2016            | Non disponible                    | 3.2.2 et 3.5.2 (SP2 CU14 et versions ultérieures) |

## <a name="python-and-r-packages"></a>Packages Python et R

Il n’y a pas de prise en charge dans SQL Managed Instance des packages qui dépendent de runtimes externes (comme Java) ou dont l’installation ou l’utilisation nécessitent un accès à des API de système d’exploitation.

Pour plus d'informations sur la gestion des packages Python et R, consultez :

- [Obtenir des informations sur les packages Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Obtenir des informations sur les packages R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Gouvernance des ressources

Dans SQL Managed Instance, il n’est pas possible de limiter les ressources R via [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true), et les pools de ressources externes ne sont pas pris en charge.

Par défaut, les ressources R sont définies sur un maximum de 20 % des ressources SQL Managed Instance disponibles quand l’extensibilité est activée. Pour modifier ce pourcentage par défaut, créez un ticket de support Azure à l’adresse [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/).

L’extensibilité est activée avec les commandes SQL suivantes (SQL Managed Instance redémarre et n’est pas disponible pendant quelques secondes) :

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Pour désactiver l’extensibilité et restaurer 100 % de la mémoire et des ressources processeur pour SQL Server, utilisez les commandes suivantes :

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

Le nombre total de ressources disponibles pour SQL Managed Instance dépend du niveau de service que vous choisissez. Pour plus d’informations, voir [Modèles d’achat Azure SQL Database](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erreur de mémoire insuffisante

L’utilisation de la mémoire dépend de la quantité de mémoire utilisée dans vos scripts R et du nombre de requêtes parallèles en cours d’exécution. Si la mémoire est insuffisante pour R, un message d’erreur s’affiche. Les messages d’erreur courants sont les suivants :

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Si vous recevez l’une de ces erreurs, vous pouvez la résoudre en mettant à l’échelle votre base de données vers un niveau de service supérieur.

## <a name="sql-managed-instance-pools"></a>Pools SQL Managed Instance

La solution Machine Learning Services n’est actuellement pas prise en charge sur les [pools Azure SQL Managed Instance (préversion)](instance-pools-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez la vue d'ensemble, [Machine Learning Services dans Azure SQL Managed Instance](machine-learning-services-overview.md).
- Pour apprendre à utiliser Python dans Machine Learning Services, consultez [Exécuter des scripts Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Pour apprendre à utiliser R dans Machine Learning Services, consultez [Exécuter des scripts R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
