---
title: Appliquer une transformation SQL
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Appliquer une transformation SQL dans Azure Machine Learning afin d’exécuter une requête SQLite sur des jeux de données d’entrée pour transformer les données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 9a195497b4376633bd3c767d7d0ea029109fdf9d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314536"
---
# <a name="apply-sql-transformation"></a>Appliquer une transformation SQL

Cet article décrit un module du concepteur Azure Machine Learning.

À l’aide du module Appliquer une transformation SQL, vous pouvez effectuer les opérations suivantes :
  
-   Créer des tables pour les résultats et enregistrer les jeux de données dans une base de données portable.  
  
-   Effectuer des transformations personnalisées sur des types de données ou créer des agrégats.  
  
-   Exécuter des instructions de requête SQL pour filtrer ou modifier des données, et retourner les résultats de requête dans une table de données.  

> [!IMPORTANT]
> Le moteur SQL utilisé dans ce module est **SQLite**. Pour plus d’informations sur la syntaxe SQLite, consultez [SQL as Understood by SQLite](https://www.sqlite.org/index.html).  

## <a name="how-to-configure-apply-sql-transformation"></a>Comment configurer le module Appliquer une transformation SQL  

Le module peut utiliser jusqu’à trois jeux de données comme entrées. Quand vous référencez les jeux de données connectés à chaque port d’entrée, vous devez utiliser les noms `t1`, `t2` et `t3`. Le numéro de table indique l’index du port d’entrée.  
  
Le paramètre restant est une requête SQL, qui utilise la syntaxe SQLite. Quand vous tapez plusieurs lignes dans la zone de texte **Script SQL**, utilisez un point-virgule pour terminer chaque instruction. Sinon, les sauts de ligne sont convertis en espaces.  

Ce module prend en charge toutes les instructions standard de la syntaxe SQLite. Pour obtenir la liste des instructions non prises en charge, consultez la section [Notes techniques](#technical-notes).

##  <a name="technical-notes"></a>Notes techniques  

Cette section contient des détails, des conseils et des réponses aux questions fréquentes concernant l’implémentation.

-   Une entrée est toujours obligatoire sur le port 1.  
  
-   Placez toujours les identificateurs de colonne qui contiennent un espace ou un autre caractère spécial entre crochets ou guillemets doubles quand vous référencez la colonne dans les clauses `SELECT` ou `WHERE`.  
  
### <a name="unsupported-statements"></a>Instructions non prises en charge  

Bien que SQLite prenne en charge la plupart des normes ANSI SQL, il n’inclut pas de nombreuses fonctionnalités prises en charge par les systèmes de base de données relationnelle du commerce. Pour plus d’informations, consultez [SQL as Understood by SQLite](http://www.sqlite.org/lang.html). Par ailleurs, gardez à l’esprit les restrictions suivantes quand vous créez des instructions SQL :  
  
- SQLite utilise le typage dynamique des valeurs, au lieu d’attribuer un type à une colonne comme dans la plupart des systèmes de base de données relationnelle. Il est faiblement typé et autorise la conversion de type implicite.  
  
- `LEFT OUTER JOIN` est implémenté, mais pas `RIGHT OUTER JOIN` ni `FULL OUTER JOIN`.  

- Vous pouvez utiliser les instructions `RENAME TABLE` et `ADD COLUMN` avec la commande `ALTER TABLE`, mais les autres clauses ne sont pas prises en charge, notamment `DROP COLUMN`, `ALTER COLUMN` et `ADD CONSTRAINT`.  
  
- Vous pouvez créer une vue dans SQLite, mais les vues suivantes sont en lecture seule. Vous ne pouvez pas exécuter d’instruction `DELETE`, `INSERT`ou `UPDATE` sur une vue. Toutefois, vous pouvez créer un déclencheur qui se déclenche à chaque tentative d’exécution de `DELETE`, `INSERT` ou `UPDATE` sur une vue, et exécuter d’autres opérations dans le corps du déclencheur.  
  

En plus de la liste des fonctions non prises en charge fournie sur le site officiel de SQLite, le wiki suivant donne une liste d’autres fonctionnalités non prises en charge : [SQLite - Unsupported SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
