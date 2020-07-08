---
title: Utilisation des procédures stockées
description: Conseils pour le développement de solutions en implémentant des procédures stockées dans un pool SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 87c7eaa57f9da87bd83f89953afc09632d42b1f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213395"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Utilisation de procédures stockées dans un pool SQL Synapse

Cet article fournit des conseils pour le développement de solutions de pool SQL en implémentant des procédures stockées.

## <a name="what-to-expect"></a>À quoi s’attendre

Le pool SQL prend en charge plusieurs fonctionnalités T-SQL utilisées dans SQL Server. Plus important encore, il existe différentes fonctions, propres à l’augmentation de la taille des instances, que nous voulons exploiter pour optimiser les performances de notre solution.

En outre, pour vous aider à maintenir l’échelle et les performances du pool SQL, il existe des fonctionnalités et des composants supplémentaires qui présentent des différences de comportement.

## <a name="introducing-stored-procedures"></a>Présentation des procédures stockées

Une procédure stockée est un excellent moyen d’encapsuler votre code SQL, qui est stocké à proximité de vos données de pool SQL. Les procédures stockées aident également les développeurs à modulariser leurs solutions en encapsulant le code sous la forme d’unités pouvant être facilement gérées, ce qui optimise la réutilisation du code. Chaque procédure stockée peut également accepter des paramètres, ce qui les rend encore plus flexibles.

Le pool SQL fournit une implémentation simplifiée et rationalisée pour les procédures stockées. La plus grande différence par rapport à SQL Server est le fait que la procédure stockée ne correspond pas à du code précompilé.

En général, pour les entrepôts de données, la durée de compilation est faible en comparaison au temps nécessaire pour exécuter des requêtes sur de grands volumes de données. Il est plus important de veiller à ce que le code de procédure stockée soit correctement optimisé pour les requêtes volumineuses.

> [!TIP]
> Le gain de temps recherché se compte en heures, en minutes et en secondes, et non en millisecondes. Par conséquent, il est utile de considérer les procédures stockées comme des conteneurs de logique SQL.

Lorsque le pool SQL exécute votre procédure stockée, les instructions SQL sont analysées, traduites et optimisées au moment de l’exécution. Lors de ce processus, chaque instruction est convertie en différentes requêtes distribuées. Le code SQL appliqué aux données est différent de la requête envoyée.

## <a name="nesting-stored-procedures"></a>Imbrication des procédures stockées

Lorsque les procédures stockées appellent d’autres procédures stockées ou exécutent un code SQL dynamique, la procédure stockée ou procédure d’appel de code centrale est considérée comme « imbriquée ».

Le pool SQL prend en charge un maximum de huit niveaux d’imbrication. À l’inverse, le niveau d’imbrication dans SQL Server est de 32.

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

Actuellement, le pool SQL ne prend pas en charge [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Par conséquent, vous devez suivre le niveau d’imbrication. Il est peu probable que vous dépassiez la limite de huit niveaux d’imbrication. Toutefois, si vous le faites, vous devez retravailler votre code pour qu’il corresponde aux niveaux d’imbrication dans cette limite.

## <a name="insertexecute"></a>INSERT... EXECUTE

Le pool SQL ne vous permet pas d’utiliser le jeu de résultats d’une procédure stockée avec une instruction INSERT. Toutefois, vous pouvez utiliser une autre approche. Pour obtenir un exemple, consultez l’article sur les [tables temporaires](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Limites

Certains aspects des procédures stockées Transact-SQL ne sont pas implémentés dans le pool SQL :

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

Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).
