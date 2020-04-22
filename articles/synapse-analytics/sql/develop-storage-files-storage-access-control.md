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
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420053"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Contrôler l’accès au compte de stockage pour SQL à la demande (préversion) dans Azure Synapse Analytics

Une requête SQL à la demande (préversion) lit les fichiers directement dans le stockage Azure. Étant donné que le compte de stockage est un objet qui est externe à la ressource SQL à la demande, vous devez fournir les informations d’identification appropriées. Un utilisateur a besoin des autorisations appropriées pour utiliser les informations d’identification exigées. Cet article décrit les types d’informations d’identification que vous pouvez utiliser et la façon dont la recherche des informations d’identification est appliquée pour les utilisateurs SQL et Azure AD.

## <a name="supported-storage-authorization-types"></a>Types d’autorisations de stockage pris en charge

Un utilisateur qui s’est connecté à une ressource SQL à la demande doit être autorisé à accéder aux fichiers présents dans le stockage Azure, et à les interroger. Trois types d’autorisations sont pris en charge :

- [Signature d’accès partagé](#shared-access-signature)
- [Identité gérée](#managed-identity)
- [Identité de l’utilisateur](#user-identity)

> [!NOTE]
> Le [pass-through Azure AD](#force-azure-ad-pass-through) est le comportement qui est utilisé par défaut quand vous créez un espace de travail. Si vous l’utilisez, vous n’avez pas besoin de créer des informations d’identification pour chaque compte de stockage accessible à l’aide des informations de connexion AD. Vous pouvez [désactiver ce comportement](#disable-forcing-azure-ad-pass-through).

Dans le tableau ci-dessous, vous trouverez les différents types d’autorisation qui sont pris en charge ou qui le seront bientôt.

| Type d’autorisation                    | *Utilisateur SQL*    | *Utilisateur Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | Prise en charge     | Prise en charge      |
| [Identité gérée](#managed-identity) | Non pris en charge | Non pris en charge  |
| [Identité de l’utilisateur](#user-identity)       | Non pris en charge | Prise en charge      |

### <a name="shared-access-signature"></a>Signature d’accès partagé

Une **signature d’accès partagé (SAS)** fournit un accès délégué aux ressources du compte de stockage. Avec une signature d’accès partagé, un utilisateur peut accorder aux clients l’accès aux ressources d’un compte de stockage sans partager les clés du compte. Une signature d’accès partagé vous fournit un contrôle précis sur le type d’accès que vous accordez aux clients qui disposent de la signature SAS, comme l’intervalle de validité, les autorisations accordées, la plage d’adresses IP acceptée ou le protocole accepté (https/http).

Vous pouvez obtenir un jeton SAS en accédant au **portail Azure -> Compte de stockage -> Signature d’accès partagé -> Configurer les autorisations -> Générer la chaîne de connexion et SAP**.

> [!IMPORTANT]
> Lorsqu’un jeton SAS est généré, son nom commence par un point d’interrogation (« ? »). Pour utiliser le jeton dans SQL à la demande, vous devez supprimer le point d’interrogation (« ? ») lors de la création des informations d’identification. Par exemple :
>
> Jeton SAS : ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Identité de l’utilisateur

L’**identité de l’utilisateur** (également appelée « pass-through ») est un type d’autorisation avec lequel l’identité de l’utilisateur Azure AD qui s’est connecté à SQL à la demande est utilisée pour autoriser l’accès aux données. Avant d’accéder aux données, l’administrateur du stockage Azure doit accorder des autorisations à l’utilisateur Azure AD. Comme indiqué dans le tableau ci-dessus, elle n’est pas prise en charge pour le type d’utilisateur SQL.

> [!NOTE]
> Si vous utilisez le [pass-through Azure AD](#force-azure-ad-pass-through), vous n’avez pas besoin de créer des informations d’identification pour chaque compte de stockage accessible à l’aide des informations de connexion AD.

> [!IMPORTANT]
> Pour utiliser votre identité afin d’accéder aux données, vous devez disposer d’un rôle de propriétaire, de contributeur ou de lecteur pour les données blob de stockage.
> Même si vous êtes propriétaire d’un compte de stockage, vous devez vous ajouter à l’un des rôles de données blob de stockage.
>
> Pour plus d’informations sur le contrôle d’accès dans Azure Data Lake Store Gen2, consultez l’article [Contrôle d’accès dans Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
>

### <a name="managed-identity"></a>Identité managée

L’**identité managée** est également connue sous le nom de MSI. Il s’agit d’une fonctionnalité d’Azure Active Directory (Azure AD) qui fournit des services Azure pour SQL à la demande. En outre, elle déploie automatiquement une identité managée dans Azure AD. Cette identité peut être utilisée pour autoriser les demandes d’accès aux données dans le stockage Azure.

Avant d’accéder aux données, l’administrateur du stockage Azure doit accorder des autorisations à l’identité managée pour accéder aux données. L’octroi d’autorisations à une identité managée s’effectue de la même façon que l’octroi d’une autorisation à un autre utilisateur Azure AD.

## <a name="create-credentials"></a>Créer des informations d’identification

Pour interroger un fichier situé dans le stockage Azure, votre point de terminaison SQL à la demande a besoin d’informations d’identification au niveau du serveur qui contiennent les informations d’authentification. Les informations d’identification sont ajoutées par l’exécution de [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Vous devez fournir un argument CREDENTIAL NAME. Il doit correspondre à une partie du chemin ou au chemin complet des données situées dans le stockage (voir ci-dessous).

> [!NOTE]
> L’argument FOR CRYPTOGRAPHIC PROVIDER n’est pas pris en charge.

Pour tous les types d’autorisation pris en charge, les informations d’identification peuvent pointer vers un compte, un conteneur, un répertoire (non racine) ou un fichier unique.

Le nom des informations d’identification doit correspondre au chemin complet du conteneur, du dossier ou du fichier, et être au format suivant : `<prefix>://<storage_account_path>/<storage_path>`

| Source de données externe       | Préfixe | Chemin de compte de stockage                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Stockage Blob Azure         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 <storage_path> est un chemin situé dans votre stockage qui pointe vers le dossier ou le fichier que vous souhaitez lire.

> [!NOTE]
> Il existe un CREDENTIAL NAME spécial `UserIdentity` qui [force le pass-through Azure AD](#force-azure-ad-pass-through). Pour plus d’informations, lisez l’article qui explique l’impact que cela a sur la [recherche d’informations d’identification](#credential-lookup) pendant l’exécution des requêtes.

Éventuellement, pour permettre à un utilisateur de créer ou de supprimer des informations d’identification, l’administrateur peut accorder (GRANT) ou refuser (DENY) l’autorisation ALTER ANY CREDENTIAL à un utilisateur :

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Types d’autorisations et de stockage pris en charge

Vous pouvez utiliser les combinaisons de types d’autorisations et de stockage Azure ci-dessous :

|                     | Stockage Blob   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Prise en charge      | Non pris en charge   | Prise en charge     |
| *Identité managée* | Non pris en charge  | Non pris en charge    | Non pris en charge |
| *Identité de l’utilisateur*    | Prise en charge      | Prise en charge        | Prise en charge     |

### <a name="examples"></a>Exemples

En fonction du [type d’autorisation](#supported-storage-authorization-types), vous pouvez créer des informations d’identification à l’aide de la syntaxe T-SQL ci-dessous.

**Signature d’accès partagé et stockage Blob**

Remplacez <*mystorageaccountname*> par le nom de votre compte de stockage, et <*mystorageaccountcontainername* par le nom du conteneur :

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Identité de l’utilisateur et Azure Data Lake Storage Gen1**

Remplacez <*mystorageaccountname*> par le nom de votre compte de stockage, et <*mystorageaccountcontainername* par le nom du conteneur :

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Identité de l’utilisateur et Azure Data Lake Storage Gen2**

Remplacez <*mystorageaccountname*> par le nom de votre compte de stockage, et <*mystorageaccountcontainername* par le nom du conteneur :

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Forcer le pass-through Azure AD

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

Pour plus d’informations sur la façon dont SQL à la demande trouve les informations d’identification à utiliser, consultez [Recherche d’informations d’identification](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Désactiver le forçage du pass-through Azure AD

Vous pouvez désactiver le [forçage du pass-through Azure AD pour chaque requête](#force-azure-ad-pass-through). Pour le désactiver, supprimez les informations d’identification `Userdentity` en utilisant ceci :

```sql
DROP CREDENTIAL [UserIdentity];
```

Si vous souhaitez le réactiver, reportez-vous à la section [Forcer le pass-through Azure AD](#force-azure-ad-pass-through).

Pour désactiver le forçage du pass-through Azure AD pour un utilisateur, vous pouvez refuser l’autorisation REFERENCE pour les informations d’identification `UserIdentity` à l’utilisateur en question. L’exemple suivant permet de désactiver le forçage du pass-through Azure AD pour un nom d’utilisateur (user_name) :

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Pour plus d’informations sur la façon dont SQL à la demande trouve les informations d’identification à utiliser, consultez [Recherche d’informations d’identification](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Accorder des autorisations pour l’utilisation d’informations d’identification

Pour utiliser des informations d’identification, un utilisateur doit disposer de l’autorisation REFERENCES sur les informations d’identification en question. Si vous souhaitez accorder une autorisation REFERENCES pour un storage_credential à un specific_user, exécutez ceci :

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Pour rendre l’expérience de pass-through Azure AD plus fluide, tous les utilisateurs auront, par défaut, le droit d’utiliser les informations d’identification `UserIdentity`. Pour ce faire, vous devez exécuter automatiquement l’instruction suivante lors du provisionnement de l’espace de travail Azure Synapse :

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Recherche des informations d’identification

Lorsque vous autorisez des requêtes, la recherche d’informations d’identification est utilisée pour accéder à un compte de stockage, et elle est basée sur les règles suivantes :

- L’utilisateur est connecté en tant qu’utilisateur Azure AD

  - Si des informations d’identification UserIdentity existent et que l’utilisateur dispose d’autorisations de référence sur celle-ci, le pass-through Azure AD sera utilisé. Si ce n’est pas le cas, [recherchez les informations d’identification à l’aide du chemin](#lookup-credential-by-path).

- L’utilisateur est connecté en tant qu’utilisateur SQL

  - Suivez les instructions fournies dans [Rechercher les informations d’identification à l’aide du chemin](#lookup-credential-by-path).

### <a name="lookup-credential-by-path"></a>Rechercher les informations d’identification à l’aide du chemin

Si le forçage du pass-through Azure AD est désactivé, la recherche d’informations d’identification sera basée sur le chemin du stockage (la profondeur en premier) et sur l’existence d’une autorisation REFERENCES sur les informations d’identification en question. Lorsque plusieurs informations d’identification peuvent être utilisées pour accéder au même fichier, SQL à la demande utilise celles qui sont les plus précises.  

Voici un exemple de requête sur le chemin de fichier suivant : *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

La recherche des informations d’identification sera effectuée dans cet ordre :

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Si un utilisateur ne dispose pas de l’autorisation REFERENCES pour les informations d’identification numéro 5, SQL à la demande vérifie que l’utilisateur dispose de l’autorisation REFERENCES pour des informations d’identification d’un niveau supérieur, jusqu’à ce qu’il trouve des informations d’identification pour lesquelles l’utilisateur dispose de l’autorisation REFERENCES. Si cette autorisation n’est pas trouvée, un message d’erreur est retourné.

### <a name="credential-and-path-level"></a>Informations d’identification et niveau du chemin

En fonction de la forme de chemin que vous souhaitez, les conditions suivantes sont exigées pour l’exécution des requêtes :

- Si la requête cible plusieurs fichiers (dossiers, avec ou sans caractères génériques), l’utilisateur doit avoir accès à des informations d’identification pour au moins le niveau du répertoire racine (au niveau du conteneur). Ce niveau d’accès est nécessaire, car les fichiers listing sont relatifs au répertoire racine (limitations du stockage Azure)
- Si la requête cible un seul fichier, l’utilisateur doit avoir accès à des informations d’identification pour n’importe quel niveau, car SQL à la demande accède directement au fichier, autrement dit, sans lister les dossiers.

|                  | *Compte* | *Répertoire racine* | *N’importe quel répertoire* | *File*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Fichier unique*    | Prise en charge | Prise en charge        | Prise en charge             | Prise en charge     |
| *Fichiers multiples* | Prise en charge | Prise en charge        | Non pris en charge         | Non pris en charge |

## <a name="next-steps"></a>Étapes suivantes

Les articles listés ci-dessous vous expliqueront comment interroger les différents types de dossiers et de fichiers, et comment créer et utiliser des vues :

- [Interroger un fichier CSV](query-single-csv-file.md)
- [Interroger des dossiers et plusieurs fichiers CSV](query-folders-multiple-csv-files.md)
- [Interroger des fichiers spécifiques](query-specific-files.md)
- [Interroger des fichiers Parquet](query-parquet-files.md)
- [Créer et utiliser des vues](create-use-views.md)
- [Interroger des fichiers JSON](query-json-files.md)
- [Interroger des types imbriqués Parquet](query-parquet-nested-types.md)
