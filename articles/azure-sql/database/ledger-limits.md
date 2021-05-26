---
title: Limitations relatives au registre Azure SQL Database
description: Limitations de la fonctionnalité de registre dans Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 1a3451356f0c38e8006b4724873f2b82f39d6a85
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386308"
---
# <a name="limitations-for-azure-sql-database-ledger"></a>Limitations relatives au registre Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en **préversion publique**.

Cet article fournit une vue d’ensemble des limitations lors de l’utilisation de tables de registre avec Azure SQL Database.  

## <a name="limitations"></a>Limites

| Fonction | Limitation |
| :--- | :--- |
| Désactivation de la [base de données du registre](ledger-database-ledger.md)   | Une fois activée, la base de données du registre ne peut pas être désactivée. |
| Nombre maximal de colonnes | Lors de leur création, les [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md) ajoutent quatre colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) à la table de registre et les [tables de registre en ajout seul](ledger-append-only-ledger-tables.md) ajoutent deux colonnes à la table de registre. Ces nouvelles colonnes sont décomptées par rapport au nombre maximal de colonnes prises en charge dans Azure SQL Database (1024). |
| Types de données restreints | Les types de données XML, SqlVariant, définis par l’utilisateur et FILESTREAM ne sont pas pris en charge. |
| Tables en mémoire | Les tables en mémoire ne sont pas prises en charge. |
| Jeux de colonnes éparses | Les jeux de colonnes éparses ne sont pas pris en charge. |
| Troncation du registre | La suppression des données anciennes dans les [tables de registre en ajout seul](ledger-append-only-ledger-tables.md) et la table d’historique dans les [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md) ne sont pas pris en charge. |
| Conversion de tables existantes en tables de registre | Les tables existantes dans une base de données qui ne sont pas compatibles avec le registre ne peuvent pas être converties en tables du registre. |
|Prise en charge de LRS pour la [gestion automatisée des synthèses](ledger-digest-management-and-database-verification.md) | La gestion automatisée des synthèses avec les tables de registre à l’aide d’[objets blob immuables Stockage Azure](../../storage/blobs/storage-blob-immutable-storage.md) n’offre pas la possibilité pour les utilisateurs d’utiliser des comptes de [stockage localement redondant (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage).|

## <a name="remarks"></a>Remarques

- Lorsqu’une base de données de registre est créée, toutes les nouvelles tables créées par défaut (sans spécifier la clause `APPEND_ONLY = ON`) dans la base de données sont des [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md). Vous pouvez créer des [tables de registre en ajout seul](ledger-append-only-ledger-tables.md) à l’aide d’instructions [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql).
- Les tables du registre ne peuvent pas être des FILETABLE.
- Les tables du registre ne peuvent pas avoir d’index de recherche en texte intégral.
- Il est impossible de renommer les tables du registre.
- Les tables du registre ne peuvent pas être déplacées vers un autre schéma.
- Seules des colonnes pouvant accepter la valeur Null peuvent être ajoutées à des tables de registre, et seulement quand WITH VALUES n’est pas spécifié.
- Les colonnes des tables du registre ne peuvent pas être supprimées.
- Seules les colonnes calculées déterministes sont autorisées pour les tables du registre.
- Les colonnes existantes ne peuvent pas être modifiées d’une manière qui modifie le format de cette colonne.
  - Nous autorisons la modification de ce qui suit :
    - Possibilité de valeurs nulles
    - Classement des colonnes nvarchar/ntext et lorsque la page de codes ne change pas pour les colonnes de type char/text
    - Modification de la longueur des colonnes de longueur variable
    - Le caractère épars
- SWITCH IN/OUT n’est pas autorisé pour les tables du registre
- Les sauvegardes à long terme (LTR) ne sont pas prises en charge pour les bases de données qui ont `LEDGER = ON`
- Vous ne pouvez pas désactiver `LEDGER` ou `SYSTEM_VERSIONING` pour les tables du registre.
- Les API `UPDATETEXT` et `WRITETEXT` ne peuvent pas être utilisées sur les tables du registre.
- Une transaction peut mettre à jour jusqu’à 200 tables de registre.
- Pour les tables de registre pouvant être mises à jour, nous héritons de toutes les limitations des tables temporelles.
- Le suivi des modifications n’est pas autorisé sur les tables du registre.
- Les tables du registre ne peuvent pas avoir d’index rowstore non mis en cluster lorsqu’elles ont un index ColumnStore en cluster.

## <a name="next-steps"></a>Étapes suivantes

- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)   
- [Tables de registre pour ajout uniquement](ledger-append-only-ledger-tables.md)   
- [Registre de base de données](ledger-database-ledger.md)   
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)   
