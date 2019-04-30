---
title: Transparent Data Encryption dans SQL Data Warehouse (T-SQL)| Microsoft Docs
description: Chiffrement transparent des données (TDE) dans SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 7908e6e0927357446ea45e16b7c44adb83ec1fd3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439058"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Prise en main du chiffrement transparent des données (TDE)
> [!div class="op_single_selector"]
> * [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
> * [Authentification](sql-data-warehouse-authentication.md)
> * [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
> * [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Autorisations nécessaires
Pour activer le chiffrement transparent des données (TDE), vous devez être un administrateur ou un membre du rôle dbmanager.

## <a name="enabling-encryption"></a>Activation du chiffrement
Pour activer le chiffrement transparent des données pour une instance SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* sur le serveur hébergeant la base de données à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Désactivation du chiffrement
Pour désactiver le chiffrement transparent des données pour une instance SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Une instance SQL Data Warehouse suspendue doit reprendre avant que des modifications ne puissent être apportées aux paramètres de chiffrement transparent des données.
> 
> 

## <a name="verifying-encryption"></a>Vérification de chiffrement
Pour vérifier l’état du chiffrement pour SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* ou d’instance à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Un résultat de ```1``` indique une base de données chiffrée, ```0``` indique une base de données non chiffrée.

## <a name="encryption-dmvs"></a>DMV de chiffrement
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->