---
title: Contrôler l’accès au compte de stockage pour SQL à la demande (préversion)
description: Explique comment SQL à la demande (préversion) accède au stockage Azure et comment vous pouvez contrôler l’accès au stockage pour SQL à la demande dans Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2d5d508afe81975cbeda448b497a098e8a3bbcf3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589276"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Contrôler l’accès au compte de stockage pour SQL à la demande (préversion)

Une requête SQL à la demande lit les fichiers directement dans Stockage Azure. Les autorisations d’accès aux fichiers sur le stockage Azure sont contrôlées à deux niveaux :
- **Niveau de stockage** : l’utilisateur doit avoir l’autorisation d’accéder aux fichiers de stockage sous-jacents. Votre administrateur de stockage doit autoriser le principal Azure AD à lire/écrire des fichiers, ou à générer une clé SAP qui sera utilisée pour accéder au stockage.
- **Niveau de service SQL** : l’utilisateur doit disposer de l’autorisation `SELECT` pour lire les données d’une [table externe](develop-tables-external-tables.md) ou de l’autorisation `ADMINISTER BULK ADMIN` pour exécuter `OPENROWSET`, ainsi que de l’autorisation d’utiliser des informations d’identification pour accéder au stockage.

Cet article décrit les types d’informations d’identification que vous pouvez utiliser et la façon dont la recherche des informations d’identification est appliquée pour les utilisateurs SQL et Azure AD.

## <a name="supported-storage-authorization-types"></a>Types d’autorisations de stockage pris en charge

Un utilisateur qui s’est connecté à une ressource SQL à la demande doit être autorisé à accéder aux fichiers présents dans le stockage Azure, ainsi qu’à les interroger si les fichiers ne sont pas publiquement disponibles. Trois types d’autorisations sont pris en charge :

- [Signature d’accès partagé](?tabs=shared-access-signature)
- [Identité de l’utilisateur](?tabs=user-identity)
- [Identité gérée](?tabs=managed-identity)

> [!NOTE]
> Le [pass-through Azure AD](#force-azure-ad-pass-through) est le comportement qui est utilisé par défaut quand vous créez un espace de travail. Si vous l’utilisez, vous n’avez pas besoin de créer des informations d’identification pour chaque compte de stockage accessible à l’aide d’informations de connexion Azure AD. Vous pouvez [désactiver ce comportement](#disable-forcing-azure-ad-pass-through).

### <a name="shared-access-signature"></a>[Signature d’accès partagé](#tab/shared-access-signature)

Une **signature d’accès partagé (SAS)** fournit un accès délégué aux ressources du compte de stockage. Avec une signature d’accès partagé, un utilisateur peut accorder aux clients l’accès aux ressources d’un compte de stockage sans partager les clés du compte. Une signature d’accès partagé vous fournit un contrôle précis sur le type d’accès que vous accordez aux clients qui disposent de la signature SAS, comme l’intervalle de validité, les autorisations accordées, la plage d’adresses IP acceptée ou le protocole accepté (https/http).

Vous pouvez obtenir un jeton SAS en accédant au **portail Azure -> Compte de stockage -> Signature d’accès partagé -> Configurer les autorisations -> Générer la chaîne de connexion et SAP**.

> [!IMPORTANT]
> Lorsqu’un jeton SAS est généré, son nom commence par un point d’interrogation (« ? »). Pour utiliser le jeton dans SQL à la demande, vous devez supprimer le point d’interrogation (« ? ») lors de la création des informations d’identification. Par exemple :
>
> Jeton SAS : ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Vous devez créer des informations d’identification incluses dans l’étendue de la base de données ou du serveur pour autoriser l’accès à l’aide du jeton SAP.

### <a name="user-identity"></a>[Identité de l’utilisateur](#tab/user-identity)

L’**identité de l’utilisateur** (également appelée « pass-through ») est un type d’autorisation avec lequel l’identité de l’utilisateur Azure AD qui s’est connecté à SQL à la demande est utilisée pour autoriser l’accès aux données. Avant d’accéder aux données, l’administrateur du stockage Azure doit accorder des autorisations à l’utilisateur Azure AD. Comme indiqué dans le tableau ci-dessus, elle n’est pas prise en charge pour le type d’utilisateur SQL.

> [!IMPORTANT]
> Pour utiliser votre identité afin d’accéder aux données, vous devez disposer d’un rôle de propriétaire, de contributeur ou de lecteur pour les données blob de stockage.
> Même si vous êtes propriétaire d’un compte de stockage, vous devez vous ajouter à l’un des rôles de données blob de stockage.
>
> Pour plus d’informations sur le contrôle d’accès dans Azure Data Lake Store Gen2, consultez l’article [Contrôle d’accès dans Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
>

Vous devez activer explicitement l’authentification directe Azure AD pour permettre aux utilisateurs Azure AD d’accéder au stockage à l’aide de leurs identités.

#### <a name="force-azure-ad-pass-through"></a>Forcer le pass-through Azure AD

Le fait de forcer un pass-through Azure AD est un comportement par défaut qui s’obtient par l’utilisation d’un CREDENTIAL NAME spécial (`UserIdentity`), qui est créé automatiquement lors du provisionnement de l’espace de travail Azure Synapse. Celui-ci force l’utilisation d’un pass-through Azure AD pour chaque requête de chaque connexion Azure AD, ce qui se produit en dépit de l’existence d’autres informations d’identification.

> [!NOTE]
> Le pass-through Azure AD est un comportement par défaut. Vous n’avez pas besoin de créer des informations d’identification pour chaque compte de stockage accessible à l’aide des informations de connexion AD.

Si vous avez [désactivé le forçage du pass-through Azure AD pour chaque requête](#disable-forcing-azure-ad-pass-through) et souhaitez le réactiver, exécutez ceci :

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Pour activer le forçage du pass-through Azure AD pour un utilisateur, vous pouvez accorder l’autorisation REFERENCE pour les informations d’identification `UserIdentity` à l’utilisateur en question. L’exemple suivant permet de forcer un pass-through Azure AD pour un nom d’utilisateur (user_name) :

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Désactiver le forçage du pass-through Azure AD

Vous pouvez désactiver le [forçage du pass-through Azure AD pour chaque requête](#force-azure-ad-pass-through). Pour le désactiver, supprimez les informations d’identification `Userdentity` en utilisant ceci :

```sql
DROP CREDENTIAL [UserIdentity];
```

Si vous souhaitez le réactiver, reportez-vous à la section [Forcer le pass-through Azure AD](#force-azure-ad-pass-through).

### <a name="managed-identity"></a>[Identité gérée](#tab/managed-identity)

L’**identité managée** est également connue sous le nom de MSI. Il s’agit d’une fonctionnalité d’Azure Active Directory (Azure AD) qui fournit des services Azure pour SQL à la demande. En outre, elle déploie automatiquement une identité managée dans Azure AD. Cette identité peut être utilisée pour autoriser les demandes d’accès aux données dans le stockage Azure.

Avant d’accéder aux données, l’administrateur du stockage Azure doit accorder des autorisations à l’identité managée pour accéder aux données. L’octroi d’autorisations à une identité managée s’effectue de la même façon que l’octroi d’une autorisation à un autre utilisateur Azure AD.

### <a name="anonymous-access"></a>[Accès anonyme](#tab/public-access)

Vous pouvez accéder aux fichiers publiquement disponibles placés sur des comptes de stockage Azure qui [autorisent l’accès anonyme](/azure/storage/blobs/storage-manage-access-to-resources.md).

---

### <a name="supported-authorization-types-for-databases-users"></a>Types d’autorisation pris en charge pour les utilisateurs de bases de données

Le tableau ci-dessous présente les types d’autorisation disponibles :

| Type d’autorisation                    | *Utilisateur SQL*    | *Utilisateur Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Identité de l’utilisateur](?tabs=user-identity#supported-storage-authorization-types)       | Non pris en charge | Prise en charge      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Prise en charge     | Prise en charge      |
| [Identité gérée](?tabs=managed-identity#supported-storage-authorization-types) | Non pris en charge | Prise en charge      |

### <a name="supported-storages-and-authorization-types"></a>Types d’autorisations et de stockage pris en charge

Vous pouvez utiliser les combinaisons de types d’autorisations et de stockage Azure ci-dessous :

|                     | Stockage Blob   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Prise en charge      | Non pris en charge   | Prise en charge     |
| *Identité managée* | Prise en charge      | Prise en charge        | Prise en charge     |
| *Identité de l’utilisateur*    | Prise en charge      | Prise en charge        | Prise en charge     |

## <a name="credentials"></a>Informations d'identification

Pour interroger un fichier situé dans Stockage Azure, votre point de terminaison SQL à la demande a besoin d’informations d’identification qui contiennent les informations d’authentification. Deux types d’informations d’identification sont utilisés :
- Les INFORMATIONS D’IDENTIFICATION au niveau du serveur sont utilisées pour des requêtes ad hoc exécutées à l’aide de la fonction `OPENROWSET`. Le nom des informations d’identification doit correspondre à l’URL de stockage.
- Les INFORMATIONS D’IDENTIFICATION INCLUSES DANS l’étendue de la base de données sont utilisées pour des tables externes. Une table externe fait référence au paramètre `DATA SOURCE` avec les informations d’identification à utiliser pour accéder au stockage.

Pour permettre à un utilisateur de créer ou de supprimer des informations d’identification, l’administrateur peut accorder (GRANT) ou refuser (DENY) l’autorisation Modifier des informations d’identification (ALTER ANY CREDENTIAL) à un utilisateur :

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Les utilisateurs de base de données qui accèdent à un stockage externe doivent avoir l’autorisation d’utiliser des informations d’identification.

### <a name="grant-permissions-to-use-credential"></a>Accorder des autorisations pour l’utilisation d’informations d’identification

Pour utiliser des informations d’identification, un utilisateur doit disposer de l’autorisation `REFERENCES` sur des informations d’identification spécifiques. Si vous souhaitez accorder une autorisation `REFERENCES` SUR un storage_credential à un specific_user, exécutez la commande suivante :

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Pour rendre l’expérience de pass-through Azure AD plus fluide, tous les utilisateurs auront, par défaut, le droit d’utiliser les informations d’identification `UserIdentity`. Pour ce faire, vous devez exécuter automatiquement l’instruction suivante lors du provisionnement de l’espace de travail Azure Synapse :

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="server-scoped-credential"></a>Informations d’identification incluses dans l’étendue du serveur

Les informations d’identification incluses dans l’étendue du serveur sont utilisées lorsque la connexion SQL appelle la fonction `OPENROWSET` sans le paramètre `DATA_SOURCE` pour lire des fichiers sur un compte de stockage. Le nom des informations d’identification incluses dans l’étendue du serveur **doit** correspondre à l’URL du stockage Azure. Les informations d’identification sont ajoutées par l’exécution de [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Vous devez fournir un argument CREDENTIAL NAME. Il doit correspondre à une partie du chemin ou au chemin complet des données situées dans le stockage (voir ci-dessous).

> [!NOTE]
> L’argument FOR CRYPTOGRAPHIC PROVIDER n’est pas pris en charge.

Le nom des INFORMATIONS D’IDENTIFICATION au niveau du serveur doit correspondre au chemin d’accès complet au compte de stockage (et éventuellement au conteneur) dans le format suivant : `<prefix>://<storage_account_path>/<storage_path>`. Les chemins d’accès de compte de stockage sont décrits dans le tableau suivant :

| Source de données externe       | Préfixe | Chemin de compte de stockage                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Stockage Blob Azure         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

> [!NOTE]
> Il existe au niveau du serveur des INFORMATIONS D’IDENTIFICATION `UserIdentity` spécifiques qui [forcent l’accès direct Azure AD](?tabs=user-identity#force-azure-ad-pass-through).

Les informations d’identification informations d’identification incluses dans l’étendue du serveur permettent d’accéder au stockage Azure à l’aide des types d’authentification suivants :

### <a name="shared-access-signature"></a>[Signature d’accès partagé](#tab/shared-access-signature)

Le script suivant crée des informations d’identification au niveau du serveur que la fonction `OPENROWSET` peut utiliser pour accéder à n’importe quel fichier sur un stockage Azure à l’aide d’un jeton SAP. Créez ces informations d’identification pour permettre au principal SQL qui exécute la fonction `OPENROWSET` de lire des fichiers protégés par la clé SAP sur le stockage Azure qui correspond à l’URL dans le nom des informations d’identification.

Remplacez <*mystorageaccountname*> par le nom de votre compte de stockage, et <*mystorageaccountcontainername* par le nom du conteneur :

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="user-identity"></a>[Identité de l’utilisateur](#tab/user-identity)

Le script suivant crée des informations d’identification au niveau du serveur qui permettent à l’utilisateur d’emprunter une identité en utilisant une identité Azure AD.

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

### <a name="managed-identity"></a>[Identité gérée](#tab/managed-identity)

Le script suivant crée des informations d’identification au niveau du serveur que la fonction `OPENROWSET` peut utiliser pour accéder à n’importe quel fichier sur un stockage Azure à l’aide d’une identité managée d’espace de travail.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Accès public](#tab/public-access)

Le script suivant crée des informations d’identification au niveau du serveur que la fonction `OPENROWSET` peut utiliser pour accéder à n’importe quel fichier sur un stockage Azure publiquement disponible. Créez ces informations d’identification pour permettre au principal SQL qui exécute la fonction `OPENROWSET` de lire des fichiers publiquement disponibles sur le stockage Azure qui correspond à l’URL dans le nom des informations d’identification.

Vous devriez remplacer <*mystorageaccountname*> par le nom de votre compte de stockage, et <*mystorageaccountcontainername*> par le nom du conteneur réel :

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```
---

## <a name="database-scoped-credential"></a>Informations d’identification incluses dans l’étendue de la base de données

Les informations d’identification incluses dans l’étendue de la base de données sont utilisées quand un principal appelle la fonction `OPENROWSET` avec le paramètre `DATA_SOURCE` ou sélectionne des données d’une [table externe](develop-tables-external-tables.md) qui n’accèdent pas aux fichiers publics. Les informations d’identification incluses dans l’étendue de la base de données ne doivent pas nécessairement correspondre au nom du compte de stockage, car elles seront utilisées de manière explicite dans la SOURCE DE DONNÉES qui définit l’emplacement de stockage.

Les informations d’identification informations d’identification incluses dans l’étendue de la base de données permettent d’accéder au stockage Azure à l’aide des types d’authentification suivants :

### <a name="shared-access-signature"></a>[Signature d’accès partagé](#tab/shared-access-signature)

Le script suivant crée des informations d’identification qui sont utilisées pour accéder aux fichiers sur le stockage en utilisant le jeton SAP spécifié dans les informations d’identification.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="azure-ad-identity"></a>[Identité Azure AD](#tab/user-identity)

Le script suivant crée des informations d’identification incluses dans l’étendue de la base de données qui sont utilisées par la [table externe](develop-tables-external-tables.md), et les fonctions `OPENROWSET` qui utilisent la source de données avec des informations d’identification pour accéder aux fichiers de stockage à l’aide de leur propre identité Azure AD.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

### <a name="managed-identity"></a>[Identité gérée](#tab/managed-identity)

Le script suivant crée des informations d’identification incluses dans l’étendue de la base de données, qui peuvent être utilisées pour emprunter l’identité de l’utilisateur Azure AD actuel en tant qu’identité managée du service. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

Les informations d’identification incluses dans l’étendue de la base de données ne doivent pas nécessairement correspondre au nom du compte de stockage, car elles seront utilisées de manière explicite dans la SOURCE DE DONNÉES qui définit l’emplacement de stockage.

### <a name="public-access"></a>[Accès public](#tab/public-access)

Les informations d’identification incluses dans l’étendue de la base de données ne sont pas requises pour autoriser l’accès aux fichiers publiquement disponibles. Créez une [source de données sans informations d’identification incluses dans l’étendue de la base de données](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) pour accéder aux fichiers publiquement disponibles sur le stockage Azure.

---

Les informations d’identification incluses dans l’étendue de la base de données sont utilisées dans des sources de données externes pour spécifier la méthode d’authentification qui sera utilisée pour accéder à ce stockage :

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exemples

**Accès à la source de données publiquement disponible**

Utilisez le script suivant pour créer une table qui accède à la source de données publiquement disponible.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://****.blob.core.windows.net/public-access' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [publicData], FILE_FORMAT = [SynapseParquetFormat] )
```

L’utilisateur de base de données peut lire le contenu des fichiers à partir de la source de données à l’aide d’une table externe ou de la fonction [OPENROWSET](develop-openrowset.md) qui fait référence à la source de données :

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

**Accès à une source de données à l’aide d’informations d’identification**

Modifiez le script suivant pour créer une table externe qui accède au stockage Azure à l’aide d’un jeton SAP, d’une identité d’utilisateur Azure AD ou d’une identité managée d’espace de travail.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use User Identity, Managed Identity, or SAS. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL MyIdentity WITH IDENTITY = 'User Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = MyIdentity 
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FILE_FORMAT = [SynapseParquetFormat] )

```

L’utilisateur de base de données peut lire le contenu des fichiers à partir de la source de données à l’aide d’une [table externe](develop-tables-external-tables.md) ou de la fonction [OPENROWSET](develop-openrowset.md) qui fait référence à la source de données :

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

## <a name="next-steps"></a>Étapes suivantes

Les articles listés ci-dessous vous expliqueront comment interroger les différents types de dossiers et de fichiers, et comment créer et utiliser des vues :

- [Interroger un fichier CSV](query-single-csv-file.md)
- [Interroger des dossiers et plusieurs fichiers CSV](query-folders-multiple-csv-files.md)
- [Interroger des fichiers spécifiques](query-specific-files.md)
- [Interroger des fichiers Parquet](query-parquet-files.md)
- [Créer et utiliser des vues](create-use-views.md)
- [Interroger des fichiers JSON](query-json-files.md)
- [Interroger des types imbriqués Parquet](query-parquet-nested-types.md)
