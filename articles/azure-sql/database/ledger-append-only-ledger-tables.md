---
title: Tables de registre d’ajout uniquement dans Azure SQL Database
description: Cet article fournit des informations sur le schéma et les vues de table de registre d’ajout uniquement dans Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 53783152a7968fff5375be82c9b72f0881c349c4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386368"
---
# <a name="azure-sql-database-append-only-ledger-tables"></a>Tables de registre d’ajout uniquement dans Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le registre Azure SQL Database est actuellement en **préversion publique**.

Les tables de registre d’ajout uniquement autorisent seulement les opérations `INSERT` sur vos tables, ce qui garantit que les utilisateurs privilégiés, par exemple, les administrateurs de base de données, ne peuvent pas modifier les données à l’aide d’opérations de [langage de manipulation de données (DML)](/sql/t-sql/queries/queries) traditionnelles. Les tables de registre d’ajout uniquement sont idéales pour les systèmes qui ne mettent pas à jour ni ne suppriment les enregistrements, par exemple, les systèmes d’informations de sécurité et gestion d’événements (SIEM) ou les systèmes blockchain dans lesquels les données doivent être répliquées à partir du blockchain sur une base de données.  Comme aucune opération `UPDATE` ou `DELETE` n’est effectuée sur une table d’ajout uniquement, vous n’avez pas besoin de la table d’historique correspondante, contrairement aux [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md).

:::image type="content" source="media/ledger/ledger-table-architecture-append-only.png" alt-text="architecture des tables de registre":::

La création d’une table de registre d’ajout uniquement peut être effectuée en spécifiant l’argument `LEDGER = ON` dans votre instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) avec l’option `APPEND_ONLY = ON`.

> [!IMPORTANT]
> Une fois la table créée comme table de registre, elle ne peut pas être redéfinie comme une table sans fonctionnalités de registre. Cela permet de la protéger contre un attaquant qui voudrait temporairement supprimer les fonctionnalités de registre pour effectuer des changements sur la table, puis réactiver les fonctionnalités de registre.

### <a name="append-only-ledger-table-schema"></a>Schéma de la table de registre d’ajout uniquement

Une table d’ajout uniquement doit avoir les colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) suivantes qui contiennent des métadonnées indiquant les transactions qui ont modifié la table et l’ordre des opérations par lesquelles les lignes ont été mises à jour par la transaction. Quand vous créez une table de registre d’ajout uniquement, des colonnes `GENERATED ALWAYS` sont créées dans votre table de registre. Ces données sont utiles pour comprendre comment les données ont été insérées au fil du temps.

Si vous ne spécifiez pas les définitions des colonnes `GENERATED ALWAYS` dans l’instruction [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql), le système les ajoute automatiquement, en utilisant les noms par défaut ci-dessous.

| Nom de colonne par défaut | Type de données | Description |
|--|--|--|
| ledger_start_transaction_id | bigint | ID de la transaction qui a créé une version de ligne. |
| ledger_start_sequence_number | bigint | Numéro de séquence d’une opération au sein d’une transaction qui a créé une version de ligne. |

## <a name="ledger-view"></a>Vue de registre

Pour chaque table de registre d’ajout uniquement, le système génère automatiquement une vue, appelée vue de registre. La vue de registre indique toutes les insertions de ligne qui se sont produites sur la table. La vue de registre est principalement utile pour les [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md), plutôt que pour les tables de registre d’ajout uniquement, car ces dernières n’ont pas de fonctionnalités `UPDATE` ou `DELETE`. La vue de registre des tables de registre d’ajout uniquement est disponible pour assurer la cohérence entre les tables de registre d’ajout uniquement et les tables pouvant être mises à jour.

### <a name="ledger-view-schema"></a>Schéma de la vue de registre

> [!NOTE]
> Les noms des colonnes de la vue de registre peuvent être personnalisés au moment de la création de la table à l’aide du paramètre `<ledger_view_option>` avec l’instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true). Pour plus d’informations, consultez [Options de la vue de registre](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) et les exemples correspondants dans [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true).

| Nom de colonne par défaut | Type de données | Description |
| --- | --- | --- |
| ledger_transaction_id | bigint | ID de la transaction qui a créé ou supprimé une version de ligne. |
| ledger_sequence_number | bigint | Numéro de séquence d’une opération au niveau des lignes dans la transaction sur la table. |
| ledger_operation_type_id | TINYINT | Contient `0` (**INSERT**) ou `1` (**DELETE**). L’insertion d’une ligne dans la table de registre produit une nouvelle ligne dans la vue de registre qui contient `0` dans cette colonne. La suppression d’une ligne dans la table de registre produit une nouvelle ligne dans la vue de registre qui contient `1` dans cette colonne. La mise à jour d’une ligne dans la table de registre produit deux nouvelles lignes dans la vue de registre. Une ligne qui contient `1` (**DELETE**) et une autre ligne qui contient `1` (**INSERT**) dans cette colonne. Une opération DELETE ne doit pas avoir lieu sur une table de registre d’ajout uniquement. |
| ledger_operation_type_desc | nvarchar(128) | Contient `INSERT` ou `DELETE`. Consultez les détails ci-dessus. |

## <a name="next-steps"></a>Étapes suivantes
  
- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md)
- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md)
