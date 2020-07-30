---
title: Se connecter à Synapse SQL avec sqlcmd
description: Utilisez l’utilitaire de ligne de commande sqlcmd pour établir une connexion avec SQL à la demande (préversion) et le pool SQL afin de les interroger.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 1af42a838463132a241447c11b90bfb489f879ac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059460"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Se connecter à Synapse SQL avec sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio (préversion)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Vous pouvez utiliser l’utilitaire de ligne de commande [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour établir une connexion avec SQL à la demande (préversion) et le pool SQL afin de les interroger au sein de Synapse SQL.  

## <a name="1-connect"></a>1. Se connecter
Pour commencer à utiliser [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ouvrez l’invite de commandes et entrez **sqlcmd** suivi de la chaîne de connexion de votre base de données Synapse SQL. La chaîne de connexion requiert les paramètres suivants :

* **Serveur (-S) :** Serveur, sous la forme `<`Nom_serveur`>`.database.windows.net
* **Base de données (-d) :** Nom de la base de données
* **Activer les identificateurs entre guillemets (-I) :** Les identificateurs entre guillemets doivent être activés pour permettre la connexion à une instance Synapse SQL.

Pour utiliser l’authentification SQL Server, vous devez ajouter les paramètres du nom d’utilisateur et du mot de passe :

* **Utilisateur (-U) :** Utilisateur du serveur sous la forme `<`Utilisateur`>`
* **Mot de passe (-P) :** Mot de passe associé à l’utilisateur

Par exemple, votre chaîne de connexion peut ressembler à ceci :

**SQL à la demande**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Pool SQL**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Pour utiliser l’authentification Azure Active Directory intégrée, vous devez ajouter les paramètres Azure Active Directory :

* **Authentification Azure Active Directory (-G) :** utilisez Azure Active Directory pour l’authentification

Par exemple, votre chaîne de connexion peut ressembler à ceci :

**SQL à la demande**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Pool SQL**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Vous devez [activer l’authentification Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) pour vous authentifier à l’aide d’Active Directory.

## <a name="2-query"></a>2. Requête

### <a name="use-sql-pool"></a>Utiliser un pool SQL

Une fois la connexion établie, vous pouvez envoyer toute instruction [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) prise en charge à l’instance. Dans cet exemple, les requêtes sont envoyées de manière interactive :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Pour le pool SQL, les exemples suivants montrent comment exécuter des requêtes en mode batch à l’aide de l’option -Q ou comment diriger SQL vers sqlcmd :

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>Utiliser SQL à la demande

Une fois la connexion établie, vous pouvez envoyer des instructions [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) prises en charge à l’instance.  Dans l’exemple suivant, les requêtes sont envoyées en mode interactif :

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Pour SQL à la demande, les exemples suivants montrent comment exécuter des requêtes en mode batch à l’aide de l’option -Q ou comment diriger SQL vers sqlcmd :

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les options sqlcmd, consultez la [documentation relative à sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
