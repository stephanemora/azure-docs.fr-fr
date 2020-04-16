---
title: Utilisation du langage SQL dynamique
description: Conseils pour les solutions de développement utilisant le SQL dynamique dans le pool SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633524"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>SQL dynamique dans le pool SQL Synapse

Cet article contient des conseils pour les solutions de développement utilisant le SQL dynamique dans le pool SQL.

## <a name="dynamic-sql-example"></a>Exemple de SQL dynamique

Quand vous développez le code d’une application pour un pool SQL, vous pouvez avoir besoin d’utiliser un SQL dynamique de façon à offrir des solutions flexibles, génériques et modulaires. Le pool SQL ne prend pas en charge les types de données blob pour le moment.

Le fait de ne pas prendre en charge les types de données blob peut limiter la taille de vos chaînes, car les types de données blob incluent les types varchar(max) et nvarchar(max).

Si vous avez utilisé ces types dans votre code d’application pour générer de grandes chaînes, vous devez casser le code en blocs et utiliser l’instruction EXEC à la place.

Voici un exemple simple :

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la chaîne est courte, vous pouvez utiliser [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) normalement.

> [!NOTE]
> Les instructions exécutées en tant que SQL dynamique sont toujours soumises à l’ensemble des règles de validation T-SQL.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).
