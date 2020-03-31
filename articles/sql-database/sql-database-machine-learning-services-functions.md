---
title: Écrire des fonctions R avancées
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Découvrez comment écrire une fonction R pour un calcul statistique avancé dans Azure SQL Database à l’aide de Machine Learning Services (préversion).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702450"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)

Cet article décrit comment incorporer des fonctions mathématiques et utilitaires R dans une procédure stockée SQL. Les fonctions statistiques avancées qui sont difficiles à implémenter dans T-SQL peuvent être créées dans R avec une seule ligne de code.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

- Pour exécuter l’exemple de code dans ces exercices, vous devez tout d’abord disposer d’une base de données Azure SQL avec Machine Learning Services (y compris R) activé. Pendant la préversion publique, Microsoft vous intégrera et activera le machine learning sur votre base de données nouvelle ou existante. Suivez les étapes pour vous [inscrire à la préversion](sql-database-machine-learning-services-overview.md#signup).

- Vérifiez que vous avez installé la dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Vous pouvez exécuter des scripts R à l’aide d’autres outils de gestion de base de données ou de requête, mais dans ce démarrage rapide, vous allez utiliser SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Créer une procédure stockée pour générer des nombres aléatoires

Par souci de simplicité, nous allons utiliser le package `stats` R installé et chargé par défaut avec Azure SQL Database à l’aide de Machine Learning Services (préversion). Le package contient des centaines de fonctions pour des tâches statistiques courantes, notamment la fonction `rnorm`. Cette fonction génère un nombre spécifié de nombres aléatoires à l’aide de la distribution normale, étant donné un écart type et des moyennes.

Par exemple, le code R suivant retourne 100 nombres sur une moyenne de 50, compte tenu d’un écart type de 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Pour appeler cette ligne de code R à partir de T-SQL, exécutez `sp_execute_external_script` et ajoutez la fonction R dans le paramètre de script R, comme ceci :

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Que se passe-t-il si vous voulez simplifier la génération d’un autre ensemble de nombres alétaoires ?

Rien de plus facile avec une combinaison avec SQL. Vous définissez une procédure stockée qui obtient les arguments de l’utilisateur, puis transmettez ces arguments dans le script R en tant que variables.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- La première ligne définit chacun des paramètres d’entrée SQL exigés quand la procédure stockée est exécutée.

- La ligne commençant par `@params` définit toutes les variables utilisées par le code R et les types de données SQL correspondants.

- Les lignes qui suivent immédiatement mappent les noms de paramètres SQL aux noms de variables R correspondants.

Maintenant que vous avez wrappé la fonction R dans une procédure stockée, vous pouvez facilement l’appeler et passer des valeurs différentes, comme ceci :

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Utiliser des fonctions utilitaires R à des fins de résolution des problèmes

Le package `utils`, installé par défaut, fournit une variété de fonctions utilitaires pour examiner l’environnement R actuel. Ces fonctions peuvent s’avérer utiles si vous constatez des différences dans la manière dont votre code R se comporte dans SQL et dans des environnements extérieurs. Par exemple, vous pouvez utiliser la fonction `memory.limit()` R pour obtenir la mémoire de l’environnement R actuel.

Étant donné que le package `utils` est installé mais pas chargé par défaut, vous devez utiliser la fonction `library()` pour le charger dans un premier temps.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> De nombreux utilisateurs aiment utiliser les fonctions de minutage système dans R, comme `system.time` et `proc.time`, pour capturer l’heure utilisée par les processus R et analyser les problèmes de performances.
