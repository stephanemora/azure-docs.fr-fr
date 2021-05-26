---
title: Gestion des synthèses et vérification de base de données
description: Cet article fournit des informations sur la synthèse d’une base de données de registre et la vérification de base de données dans Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: cb5c0d4b6a569e93052c2923b609935cd3934ec8
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386331"
---
# <a name="digest-management-and-database-verification"></a>Gestion des synthèses et vérification de base de données

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le registre Azure SQL Database est actuellement en **préversion publique**.

Le registre Azure SQL Database fournit une forme d’intégrité des données appelée « forward integrity », qui donne la preuve de la falsification des données dans vos tables de registre. Par exemple, supposons une transaction bancaire sur une table de registre qui met à jour le solde sur la valeur `x`. Si un attaquant modifie les données par la suite, entraînant le remplacement de la valeur `x` par `y`, cette activité de falsification est détectée dans la vérification de base de données.  

Le processus de vérification de base de données prend comme entrée une ou plusieurs synthèses de base de données précédemment générées, et recalcule les hachages stockés dans le registre de base de données en fonction de l’état actuel des tables de registre. Si les hachages calculés ne correspondent pas aux synthèses d’entrée, la vérification échoue, indiquant que les données ont été falsifiées, et signale toutes les incohérences détectées.

## <a name="database-digests"></a>Synthèses de base de données

Le hachage du dernier bloc dans le registre de base de données est appelé synthèse de base de données, et représente l’état de toutes les tables de registre dans la base de données au moment où le bloc a été généré. La génération d’une synthèse de base de données est efficace, car elle implique seulement le calcul des hachages des blocs qui ont récemment été ajoutés. Les synthèses de base de données peuvent être générées automatiquement par le système ou manuellement par l’utilisateur, puis être utilisées par la suite pour vérifier l’intégrité des données de la base de données. Les synthèses de base de données sont générées sous la forme d’un document JSON qui contient le hachage du dernier bloc et les métadonnées associées à l’ID de bloc. Les métadonnées contiennent notamment l’heure de génération de la synthèse et l’horodatage de validation de la dernière transaction dans ce bloc.

Le processus de vérification et l’intégrité de la base de données dépendent de l’intégrité des synthèses d’entrée. Dans ce but, les synthèses d’entrée qui sont extraites de la base de données doivent être stockées dans des stockages de confiance qui ne peuvent pas être falsifiés par les utilisateurs avec des privilèges élevés ou les attaquants du serveur Azure SQL Database.

### <a name="automatic-generation-and-storage-of-database-digests"></a>Génération et stockage automatiques des synthèses de base de données

Le registre Azure SQL Database intègre le [stockage immuable du Stockage Blob Azure](../../storage/blobs/storage-blob-immutable-storage.md) et le [Registre confidentiel Azure](/azure/confidential-ledger/) pour fournir des services de stockage sécurisé dans Azure afin de protéger les synthèses de base de données contre une falsification potentielle. Cette intégration fournit un moyen simple et économique aux utilisateurs d’automatiser la gestion des synthèses sans se soucier de leur disponibilité et de la réplication géographique. 

La configuration de la génération automatique et du stockage des synthèses de base de données peut être effectuée dans le portail Azure, PowerShell ou Azure CLI. Une fois configurées, les synthèses de base de données sont générées selon un intervalle prédéfini de 30 secondes et chargées dans le service de stockage sélectionné. Si aucune transaction ne se produit dans le système dans l’intervalle de 30 secondes, aucune synthèse de base de données n’est générée ni chargée, de sorte que les synthèses de base de données sont générées seulement quand des données ont été mises à jour dans votre base de données.

:::image type="content" source="media/ledger/automatic-digest-management.png" alt-text="activer le stockage de synthèses"::: 

> [!IMPORTANT]
> Une [stratégie d’immuabilité](../../storage/blobs/storage-blob-immutability-policies-manage.md) doit être configurée sur votre conteneur après le provisionnement, pour garantir la protection des synthèses de base de données contre la falsification.

### <a name="manual-generation-and-storage-of-database-digests"></a>Génération et stockage manuels des synthèses de base de données

Le registre Azure SQL Database autorise également les utilisateurs à générer une synthèse de base de données à la demande, de sorte qu’ils peuvent manuellement stocker la synthèse dans un service ou un appareil qu’ils considèrent comme étant une destination de stockage de confiance, par exemple, un appareil local WORM (Write Once Read Many). La génération manuelle d’une synthèse de base de données s’effectue en exécutant la procédure stockée [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) dans [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

> [!IMPORTANT]
> La génération de synthèses de base de données nécessite l’autorisation **GENERATE LEDGER DIGEST**. Pour plus d’informations sur les autorisations associées aux tables de registre, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

```sql
EXECUTE sp_generate_database_ledger_digest
```

Le jeu de résultats retourné est une seule ligne de données, qui doit être enregistrée dans l’emplacement de stockage de confiance sous la forme d’un document JSON, de la façon suivante :

```json
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    }
```

## <a name="database-verification"></a>Vérification de base de données

Le processus de vérification analyse toutes les tables d’historique et de registre, puis recalcule les hachages SHA-256 de leurs lignes et les compare aux fichiers de synthèse de base de données passés à la procédure stockée de vérification. Pour les grandes tables de registre, la vérification de base de données peut être un processus nécessitant de nombreuses ressources. Elle doit être exécutée seulement quand les utilisateurs doivent vérifier l’intégrité de leur base de données. Elle peut être exécutée toutes les heures ou tous les jours dans les cas où l’intégrité de la base de données doit être fréquemment supervisée, ou seulement quand l’organisation qui héberge les données a un audit et doit fournir une preuve de chiffrement validant l’intégrité de ses données. Pour réduire le coût de la vérification, le registre permet de vérifier chaque table de registre individuellement ou un sous-ensemble seulement du registre. 

La vérification de base de données est accomplie avec deux procédures stockées, selon que le [stockage automatique des synthèses](#database-verification-using-automatic-digest-storage) est utilisé ou que les [synthèses sont manuellement gérées](#database-verification-using-manual-digest-storage) par l’utilisateur.

> [!IMPORTANT]
> La vérification de base de données nécessite l’autorisation **VIEW LEDGER CONTENT**. Pour plus d’informations sur les autorisations associées aux tables de registre, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

### <a name="database-verification-using-automatic-digest-storage"></a>Vérification de base de données utilisant le stockage automatique des synthèses

Quand vous utilisez le stockage automatique des synthèses pour générer et stocker les synthèses de base de données, l’emplacement de stockage des synthèses est dans la vue de catalogue système [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) sous forme d’objets JSON. L’exécution de la vérification de base de données consiste à exécuter la procédure stockée système [sp_verify_database_ledger_from_digest_storage](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-from-digest-storage-transact-sql), en spécifiant les objets JSON de la vue de catalogue système [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) où les synthèses de base de données doivent être stockées (d’après la configuration). 

L’utilisation du stockage automatique des synthèses vous permet de changer les emplacements de stockage pendant le cycle de vie des tables de registre.  Par exemple, supposons que vous commencez à utiliser le stockage Blob immuable Azure pour stocker vos fichiers de synthèse, mais que par la suite vous voulez utiliser le Registre confidentiel Azure à la place, vous pouvez le faire. Ce changement d’emplacement est stocké dans [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql). Pour simplifier l’exécution de la vérification quand plusieurs emplacements de stockage de synthèses ont été utilisés, le script suivant récupère les emplacements des synthèses et exécute la vérification à l’aide de ces emplacements.

```sql
DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);
SELECT @digest_locations as digest_locations;
BEGIN TRY
    EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
    SELECT 'Ledger verification succeeded.' AS Result;
END TRY
BEGIN CATCH
    THROW;
END CATCH
```

### <a name="database-verification-using-manual-digest-storage"></a>Vérification de base de données utilisant le stockage manuel des synthèses

Quand vous utilisez le stockage manuel des synthèses pour générer et stocker les synthèses de base de données, la procédure stockée suivante est utilisée pour vérifier le registre, en ajoutant le contenu JSON de la synthèse dans la procédure stockée. Quand vous exécutez la vérification de base de données, vous pouvez choisir de vérifier toutes les tables de la base de données ou des tables spécifiques. La syntaxe ci-dessous est celle de la procédure stockée [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) :

```sql
sp_verify_database_ledger <JSON_document_containing_digests>, <table_name> 
```

L’exemple ci-dessous illustre l’exécution de la procédure stockée [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) en passant deux synthèses pour vérification : 

```sql
EXECUTE sp_verify_database_ledger N'
[
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    },
    {
        "database_name":  "ledgerdb",
        "block_id":  1,
        "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
        "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
        "digest_time":  "2020-11-12T18:43:30.4701575"
    }
]
```

Les codes de retour de `sp_verify_database_ledger` et `sp_verify_database_ledger_from_digest_storage` sont `0` (**réussite**) ou `1` (**échec**).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md)
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)   
- [Tables de registre pour ajout uniquement](ledger-append-only-ledger-tables.md)   
- [Registre de base de données](ledger-database-ledger.md)   
