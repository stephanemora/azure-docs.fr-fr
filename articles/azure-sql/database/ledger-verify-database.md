---
title: Comment vérifier une table du registre pour détecter une falsification
description: Cet article explique comment vérifier si une table Azure SQL Database a été falsifiée.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.reviewer: vanto
ms.date: 05/25/2021
ms.openlocfilehash: 6669b071d79c78b35dc2cfb94f99fa9597f44bd6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386284"
---
# <a name="how-to-verify-a-ledger-table-to-detect-tampering"></a>Comment vérifier une table du registre pour détecter une falsification

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le registre Azure SQL Database est actuellement en **préversion publique**.

Cet article explique comment vérifier l’intégrité des données dans les tables de votre registre Azure SQL Database. Si vous avez sélectionné l’option **Activer le stockage de synthèse automatique** lors de la [création de votre Azure SQL Database](ledger-create-a-single-database-with-ledger-enabled.md), suivez les instructions du portail Azure pour générer automatiquement le script Transact-SQL (T-SQL) nécessaire pour vérifier le registre de base de données dans l’[Éditeur de requête](connect-query-portal.md). Sinon, suivez les instructions T-SQL à l’aide de [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ou d’[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="prerequisite"></a>Configuration requise

- Un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).
- [Créer une base de données Azure SQL Database avec un registre activé.](ledger-create-a-single-database-with-ledger-enabled.md)
- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md) ou [Créer et utiliser des tables de registre par ajout uniquement](ledger-how-to-append-only-ledger-tables.md)

## <a name="run-ledger-verification-for-azure-sql-database"></a>Exécuter une vérification de registre pour Azure SQL Database

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Ouvrez le [portail Azure](https://portal.azure.com/), sélectionnez **Toutes les ressources**, puis recherchez la base de données que vous souhaitez vérifier. Sélectionnez cette base de données SQL.

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="Portail Azure montrant l’onglet Toutes les ressources":::

1. Dans **Sécurité**, sélectionnez l’option **Registre**.

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="Onglet sécurité du registre du portail Azure":::

1. Dans le volet **Registre**, sélectionnez le bouton **</> Vérifier la base de données**, puis l’icône **Copie** dans le texte pré-rempli dans la fenêtre.

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Bouton Vérifier la base de données du portail Azure":::

1. Ouvrez l’**Éditeur de requête** dans le menu de gauche.

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Bouton Éditeur de requête du portail Azure":::

1. Dans l’**Éditeur de requête**, collez le script T-SQL que vous avez copié à l’étape 3, puis sélectionnez **Exécuter**.

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="Exécution de l’Éditeur de requête du portail Azure pour vérifier la base de données":::

1. Une vérification réussie retourne ce qui suit dans la fenêtre **Résultats**.

   - Si votre base de données n’a pas été falsifiée, le message est le suivant :

   ```output
   Ledger verification successful
   ```

   - Si votre base de données a été falsifiée, l’erreur suivante s’affiche dans la fenêtre **Messages**.
  
   ```output
   Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
   ```

# <a name="t-sql"></a>[T-SQL](#tab/t-sql)

1. Connectez-vous à votre base de données à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou d’[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
1. Créez une requête avec l’instruction T-SQL suivante.

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. exécutez la requête. Les résultats contiennent la dernière synthèse de base de données et représentent le hachage de la base de données au point dans le temps actuel. Copiez le contenu des résultats à utiliser à l’étape suivante.

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Récupérer des résultats de synthèse à l’aide d’Azure Data Studio":::

1. Créez une requête avec l’instruction T-SQL suivante. Remplacez `<YOUR DATABASE DIGEST>` par la synthèse que vous avez copiée à l’étape précédente.

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. exécutez la requête. Les **messages** contiennent le message de réussite ci-dessous.  

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Message après l’exécution de la requête T-SQL pour la vérification du registre à l’aide d’Azure Data Studio":::

   > [!TIP]
   > L’exécution de la vérification du registre avec la dernière synthèse vérifie uniquement la base de données entre le moment où la synthèse a été générée et l’exécution de la vérification. Pour vérifier que les données historiques dans votre base de données n’ont pas été falsifiées, exécutez la vérification à l’aide de plusieurs fichiers de synthèse de base de données. Commencez par le point dans le temps pour lequel vous souhaitez vérifier la base de données. Un exemple de vérification transmettant plusieurs synthèses ressemblerait à la requête ci-dessous :

   ```
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

---

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md)
- [Registre de base de données](ledger-database-ledger.md)
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)
- [Tables de registre d’ajout uniquement](ledger-append-only-ledger-tables.md)
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Comment accéder aux synthèses stockées dans le Registre confidentiel Azure (ACL)](ledger-how-to-access-acl-digest.md)
