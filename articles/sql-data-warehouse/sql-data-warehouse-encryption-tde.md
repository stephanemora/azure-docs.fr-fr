---
title: Chiffrement transparent des données dans SQL Data Warehouse (portail) | Microsoft Docs
description: Chiffrement transparent des données (TDE) dans SQL Data Warehouse
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: e641ddb5360c18b5977fb79de41334d4216c2b90
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306185"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Mise en route avec le chiffrement transparent des données (TDE) dans SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
> * [Authentification](sql-data-warehouse-authentication.md)
> * [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
> * [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Autorisations requises
Pour activer le chiffrement transparent des données (TDE), vous devez être un administrateur ou un membre du rôle dbmanager.

## <a name="enabling-encryption"></a>Activation du chiffrement
Pour activer le chiffrement transparent des données pour SQL Data Warehouse, procédez comme suit :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans le panneau de la base de données, cliquez sur le bouton **Paramètres**
3. Sélectionnez l’option **Chiffrement transparent des données**![][1]
4. Sélectionnez le paramètre **Activé** ![][2]
5. Sélectionnez **Enregistrer**
   ![][3]  

## <a name="disabling-encryption"></a>Désactivation du chiffrement
Pour désactiver le chiffrement transparent des données pour SQL Data Warehouse, procédez comme suit :

1. Ouvrez la base de données dans le [portail Azure](https://portal.azure.com)
2. Dans le panneau de la base de données, cliquez sur le bouton **Paramètres**
3. Sélectionnez l’option **Chiffrement transparent des données**![][1]
4. Sélectionnez le paramètre **Désactivé** ![][4]
5. Sélectionnez **Enregistrer**
   ![][5]  

## <a name="encryption-dmvs"></a>DMV de chiffrement
Le chiffrement peut être vérifié avec les vues DMV suivantes :

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
