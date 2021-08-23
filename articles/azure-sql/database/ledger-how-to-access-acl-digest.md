---
title: Comment accéder aux synthèses stockées dans le Registre confidentiel Azure
description: Comment accéder aux synthèses stockées dans le Registre confidentiel Azure (ACL) avec le registre Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 3f8b5ae7c80c712c441648808f0303528bad8018
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966728"
---
# <a name="how-to-access-the-digests-stored-in-acl"></a>Comment accéder aux synthèses stockées dans ACL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en **préversion publique**.

Cet article vous montre comment accéder à une synthèse de [registre Azure SQL Database](ledger-overview.md) stockée dans un [Registre confidentiel Azure (ACL)](../../confidential-ledger/index.yml) pour bénéficier de garanties de sécurité et d’intégrité de bout en bout. Dans cet article, nous expliquerons comment vérifier l’intégrité des informations stockées.

## <a name="prerequisites"></a>Prérequis

- Python 2.7, 3.5.3 ou version ultérieure
- Avoir une instance Azure SQL Database existante avec registre activé. Consultez [Démarrage rapide : Créer une base de données Azure SQL avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md) si vous n’avez pas encore créé de base de données Azure SQL.
- [Bibliothèque cliente de Registre confidentiel Azure pour Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)
- Une instance en cours d’exécution du [Registre confidentiel Azure](../../confidential-ledger/index.yml).

## <a name="how-does-the-integration-work"></a>Fonctionnement du travail d’intégration

Le serveur Azure SQL calcule régulièrement les synthèses de la ou des [bases de données de registre](ledger-overview.md#ledger-database) et les stocke dans le Registre confidentiel Azure. À tout moment, un utilisateur peut valider l’intégrité des données en téléchargeant les synthèses à partir du Registre confidentiel Azure et en les comparant aux synthèses stockées dans le registre Azure SQL Database. Les étapes suivantes l’expliquent.

## <a name="1-find-the-digest-location"></a>1. Rechercher l’emplacement des synthèses

> [!NOTE]
> La requête renverra plus d’une ligne si plusieurs instances du Registre confidentiel Azure ont été utilisées pour stocker la synthèse. Pour chaque ligne, répétez les étapes 2 à 6 pour télécharger les synthèses à partir de toutes les instances du Registre confidentiel Azure.

Dans [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms), exécutez la requête suivante. La sortie indique le point de terminaison de l’instance du Registre confidentiel Azure dans laquelle les synthèses sont stockées.

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%
```

## <a name="2-determine-the-subledgerid"></a>2. Déterminer le Subledgerid

Nous cherchons à connaître la valeur dans la colonne path de la sortie de la requête. Elle se compose de deux parties, `host name` et `subledgerid`. Par exemple, dans l’URL `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`, `host name` est `https://contoso-ledger.confidential-ledger.azure.com` et `subledgerid` est `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`. Nous allons l’utiliser à l’étape 4 pour télécharger les synthèses.

## <a name="3-obtain-an-azure-ad-token"></a>3. Obtenir un jeton Azure AD

L’API Registre confidentiel Azure accepte un jeton du porteur Azure Active Directory (Azure AD) comme identité de l’appelant. Cette identité a besoin d’accéder au registre confidentiel via Azure Resource Manager lors de la configuration. L’utilisateur qui avait activé le registre dans SQL Database reçoit automatiquement un accès administrateur au Registre confidentiel Azure. Pour obtenir un jeton, l’utilisateur doit s’authentifier à l’aide d’[Azure CLI](/cli/azure/install-azure-cli) avec le même compte que celui utilisé avec le portail Azure. Une fois que l’utilisateur s’est authentifié, il peut utiliser [AzureCliCredential](/python/api/azure-identity/azure.identity.azureclicredential) pour récupérer un jeton de porteur et appeler l’API Registre confidentiel Azure.

Connectez-vous à Azure AD à l’aide de l’identité avec accès au registre confidentiel.

```azure-cli
az login
```

Récupérez le jeton du porteur.

```python
from azure.identity import AzureCliCredential
credential = AzureCliCredential()
```

## <a name="4-download-the-digests-from-azure-confidential-ledger"></a>4. Télécharger les synthèses à partir du Registre confidentiel Azure

Le script Python suivant télécharge les synthèses à partir du Registre confidentiel Azure. Le script utilise la [bibliothèque cliente Registre confidentiel Azure pour Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)

```python
from azure.identity import AzureCliCredential
from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

ledger_id = "contoso-ledger"
identity_server_url = "https://identity.confidential-ledger.core.azure.com"
sub_ledger_id = "sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000"
ledger_host_url = f"https://{ledger_id}.confidential-ledger.azure.com"
initial_path = f"/app/transactions?api-version=0.1-preview&subLedgerId={sub_ledger_id}"

identity_client = ConfidentialLedgerIdentityServiceClient(identity_server_url)
network_identity = identity_client.get_ledger_identity(
    ledger_id=ledger_id
)

ledger_tls_cert_file_name = f"{ledger_id}_certificate.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)

credential = AzureCliCredential()
ledger_client = ConfidentialLedgerClient(
    endpoint=ledger_host_url, 
    credential=credential,
    ledger_certificate_path=ledger_tls_cert_file_name
)

ranged_result = ledger_client.get_ledger_entries(
    sub_ledger_id=sub_ledger_id
)

entries = 0

for entry in ranged_result:
    entries += 1
    print(f"\nTransaction id {entry.transaction_id} contents: {entry.contents}")

if entries == 0:
    print("\n***No digests are found for the supplied SubledgerID.")
else:
    print("\n***No more digests were found for the supplied SubledgerID.")
```

## <a name="5-download-the-digests-from-the-sql-server"></a>5. Télécharger les synthèses à partir de SQL Server

> [!NOTE]
> Il s’agit d’un moyen de vérifier que les hachages stockés dans le registre Azure SQL Database n’ont pas changé au fil du temps. Pour un audit complet de l’intégrité du registre Azure SQL Database, consultez [Comment vérifier une table du registre pour détecter la falsification](ledger-verify-database.md).

À l’aide de [SSMS](/sql/ssms/download-sql-server-management-studio-ssms), exécutez la requête suivante. La requête retourne les synthèses des blocs de Genesis.

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="6-comparison"></a>6. Comparaison

Comparez la synthèse récupérée à partir du Registre confidentiel Azure à celle renvoyée par votre base de données SQL en utilisant `block_id` comme clé. Par exemple, la synthèse de `block_id` = `1` est la valeur de la colonne `previous_block_hash` dans la ligne `block_id`= `2`. De même, pour `block_id` = `3`, il s’agit de la valeur de la colonne `previous_block_id` dans la ligne `block_id` = `4`. Une non-correspondance dans la valeur de hachage est un indicateur de falsification de données potentielle.

Si vous suspectez une falsification de données, consultez [Comment vérifier une table du registre pour détecter la falsification](ledger-verify-database.md) afin d’effectuer un audit complet du registre Azure SQL Database.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md)
- [Registre de base de données](ledger-database-ledger.md)
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)
- [Tables de registre d’ajout uniquement](ledger-append-only-ledger-tables.md)
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Comment vérifier une table du registre pour détecter la falsification](ledger-verify-database.md)
