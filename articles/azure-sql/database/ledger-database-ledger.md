---
title: Registre de base de données
description: Cet article fournit des informations sur les tables de base de données de registre et les vues associées dans Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 031674854af41877483ece5c3969a0208a7a8167
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386343"
---
# <a name="what-is-the-database-ledger"></a>Qu’est-ce que le registre de base de données

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en **préversion publique**.

Le registre de base de données utilise logiquement un blockchain et des [structures de données d’arborescence Merkle](/archive/msdn-magazine/2018/march/blockchain-blockchain-fundamentals). Le registre de base de données capture de manière incrémentielle l’état de la base de données lorsqu’elle évolue au fil du temps, quand que des mises à jour sont effectuées sur les tables du registre. Pour ce faire, le registre de base de données stocke une entrée pour chaque transaction, en capturant les métadonnées relatives à la transaction, telles que son horodateur de validation et l’identité de l’utilisateur qui l’a exécutée, mais également la racine de l’arborescence Merkle des lignes mises à jour dans chaque table de registre. Ces entrées sont ensuite ajoutées à une structure de données inviolable pour permettre la vérification de l’intégrité à l’avenir.

:::image type="content" source="media/ledger/merkle-tree.png" alt-text="sql ledger merkle tree":::

Pour plus d’informations sur la façon dont le registre Azure SQL Database fournit l’intégrité des données, consultez [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md).

## <a name="where-are-database-transaction-and-block-data-stored"></a>Où sont stockées les données de bloc et de transaction de base de données ?

Les données relatives aux transactions et aux blocs sont stockées physiquement sous forme de lignes dans deux nouvelles vues de catalogue système :

- [**sys.database_ledger_transactions**](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) : gère une ligne avec les informations de chaque transaction dans le registre, y compris l’ID du bloc auquel cette transaction appartient et l’ordinal de la transaction dans le bloc. 
- [**sys.database_ledger_blocks**](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) : gère une ligne pour chaque bloc du registre, y compris la racine de l’arborescence Merkle sur les transactions au sein du bloc, et le hachage du bloc précédent pour former un blockchain.

Pour afficher le registre de base de données, exécutez les instructions T-SQL suivantes dans [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

> [!IMPORTANT]
> L’affichage du registre de base de données nécessite l’autorisation **VIEW LEDGER CONTENT**. Pour plus d’informations sur les autorisations associées aux tables du registre, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

```sql
SELECT * FROM sys.database_ledger_transactions
GO

SELECT * FROM sys.database_ledger_blocks
GO
```

Voici un exemple de table de registre qui se compose de quatre transactions qui constituent un bloc dans le blockchain du registre de base de données.

:::image type="content" source="media/ledger/database-ledger-1.png" alt-text="exemple de table de registre":::

Un bloc est fermé toutes les 30 secondes, ou lorsque l’utilisateur génère manuellement un résumé de la base de données par le biais de l’exécution de la procédure stockée [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql). Lorsqu’un bloc est fermé, les nouvelles transactions sont insérées dans un nouveau bloc. Le processus de génération de bloc récupère ensuite toutes les transactions qui appartiennent au bloc *fermé* à la fois à partir de la file d’attente en mémoire et de la vue de catalogue système [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql), calcule la racine de l’arborescence Merkle sur ces transactions et le hachage du bloc précédent, et conserve le bloc fermé dans la vue du catalogue système [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql). Étant donné qu’il s’agit d’une mise à jour de table ordinaire, sa durabilité est automatiquement garantie par le système. Pour conserver la chaîne unique de blocs, cette opération est à thread unique, mais elle est également efficace, car elle calcule uniquement les hachages sur les informations de transaction et se produit de façon asynchrone, ce qui n’affecte pas les performances de la transaction.   

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)
- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md) 
- [Affichages catalogue liées à la sécurité (Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)
