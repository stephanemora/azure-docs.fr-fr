---
title: Utiliser des procédures stockées
description: Conseils relatifs à l’implémentation de procédures stockées dans un pool SQL Synapse à des fins de développement de solutions.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2b106a28514179d9be43b3c56ea8030eaf7ba13f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494993"
---
# <a name="use-stored-procedures-in-sql-pool"></a>Utiliser des procédures stockées dans un pool SQL

Conseils relatifs à l’implémentation de procédures stockées dans un pool SQL Synapse à des fins de développement de solutions.

## <a name="what-to-expect"></a>À quoi s’attendre

Le pool SQL prend en charge plusieurs fonctionnalités T-SQL utilisées dans SQL Server. Plus important encore, il existe différentes fonctions, propres à l’augmentation de la taille des instances, que nous voulons exploiter pour optimiser les performances de notre solution.

Toutefois, pour assurer la mise à l’échelle et les performances du pool SQL, il existe divers mécanismes et fonctions dont le comportement présente des différences, ainsi que d’autres qui ne sont pas pris en charge.

## <a name="stored-procedures-in-sql-pool"></a>Procédures stockées dans un pool SQL

Une procédure stockée est un excellent moyen d’encapsuler votre code SQL, en le stockant à un emplacement proche de vos données au sein de l’entrepôt de données. Les procédures stockées aident les développeurs à modulariser leurs solutions en encapsulant le code sous la forme d’unités pouvant être facilement gérées, afin d’optimiser la réutilisation du code. Chaque procédure stockée peut également accepter des paramètres, ce qui les rend encore plus flexibles.

Le pool SQL fournit une implémentation simplifiée et rationalisée pour les procédures stockées. La plus grande différence par rapport à SQL Server est le fait que la procédure stockée ne correspond pas à du code précompilé. Dans les entrepôts de données, la durée de compilation est faible en comparaison au temps nécessaire pour exécuter des requêtes sur de grands volumes de données. Il est plus important de veiller à ce que le code de procédure stockée soit correctement optimisé pour les grosses requêtes. Le gain de temps recherché se compte en heures, en minutes et en secondes, et non en millisecondes. De ce fait, il est plus utile de considérer les procédures stockées comme des conteneurs de logique SQL.

Lorsque le pool SQL exécute votre procédure stockée, les instructions SQL sont analysées, traduites et optimisées au moment de l’exécution. Lors de ce processus, chaque instruction est convertie en différentes requêtes distribuées. Le code SQL appliqué aux données est différent de la requête envoyée.

## <a name="nesting-stored-procedures"></a>Imbrication des procédures stockées

Lorsque les procédures stockées appellent d’autres procédures stockées ou exécutent un code SQL dynamique, la procédure stockée ou procédure d’appel de code centrale est considérée comme « imbriquée ».

Le pool SQL prend en charge un maximum de huit niveaux d’imbrication. En cela, il diffère légèrement de SQL Server, qui prend en charge 32 niveaux d’imbrication.

L’appel de procédure stockée de premier niveau correspond au niveau d’imbrication 1.

```sql
EXEC prc_nesting
```

Si la procédure stockée effectue également un autre appel EXEC, le niveau d’imbrication passe à 2.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Si la deuxième procédure exécute du code SQL dynamique, ce niveau monte à 3.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> Le pool SQL ne prend pas en charge [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Vous devez suivre le niveau d’imbrication. Il est peu probable que vous dépassiez la limite des huit niveaux d’imbrication, mais si c’est le cas, vous devez revoir votre code pour ramener les niveaux d’imbrication dans cette limite.

## <a name="insertexecute"></a>INSERT... EXECUTE

Le pool SQL ne vous permet pas d’utiliser le jeu de résultats d’une procédure stockée avec une instruction INSERT. Toutefois, vous pouvez utiliser une autre méthode. Pour obtenir un exemple, consultez l’article sur les [tables temporaires](develop-tables-temporary.md).

## <a name="limitations"></a>Limites

Certains aspects des procédures stockées Transact-SQL ne sont pas implémentés dans le pool SQL.

Il s'agit de :

* Procédures stockées temporaires
* Procédures stockées numérotées
* Procédures stockées étendues
* Procédures stockées CLR
* Option de chiffrement
* Option de réplication
* Paramètres table
* Paramètres en lecture seule
* Paramètres par défaut
* Contextes d’exécution
* Instruction RETURN

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](develop-overview.md).
