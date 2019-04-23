---
title: Écrire des fonctions avancées de R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Découvrez comment écrire une fonction R pour le calcul de statistiques avancée dans la base de données SQL Azure à l’aide des Services Machine Learning (version préliminaire).
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
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014271"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Écrire des fonctions avancées de R dans la base de données SQL Azure à l’aide des Services Machine Learning (version préliminaire)

Cet article décrit comment incorporer R mathématique et fonctions utilitaires dans une instance SQL de procédure stockée. Fonctions statistiques avancées qui sont difficiles à implémenter dans T-SQL peuvent être effectuées dans R avec uniquement une seule ligne de code.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

- Pour exécuter l’exemple de code dans ces exercices, vous devez tout d’abord disposer d’Azure SQL database avec Machine Learning Services (avec R) activée. Pendant la préversion publique, Microsoft vous intégrera et activera le machine learning sur votre base de données nouvelle ou existante. Suivez les étapes pour vous [inscrire à la préversion](sql-database-machine-learning-services-overview.md#signup).

- Assurez-vous que vous avez installé la dernière version [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Vous pouvez exécuter des scripts R à l’aide d’autres tâches de gestion de base de données ou des outils de requête, mais dans ce démarrage rapide, vous allez utiliser SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Créer une procédure stockée pour générer des nombres aléatoires

Par souci de simplicité, nous allons utiliser le R `stats` package qui a installé et chargé par défaut avec la base de données SQL Azure à l’aide des Services Machine Learning (version préliminaire). Le package contient des centaines de fonctions pour des tâches statistiques courantes, nous citerons entre le `rnorm` (fonction). Cette fonction génère un nombre spécifié de nombres aléatoires à l’aide de la distribution normale, étant donné un écart type et moyen.

Par exemple, le code R suivant retourne 100 nombres sur une moyenne de 50, compte tenu d’un écart type égal à 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Pour appeler cette ligne de R à partir de T-SQL, exécutez `sp_execute_external_script` et ajoutez la fonction R dans le paramètre de script R, comme ceci :

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Que se passe-t-il si vous souhaitez rendre plus simple de générer un ensemble de nombres aléatoires différent ?

C’est facile lorsqu’elles sont combinées avec SQL. Vous définissez une procédure stockée qui obtienne les arguments de l’utilisateur, puis passer ces arguments dans le script R en tant que variables.

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

- La première ligne définit chacun des paramètres d’entrée SQL qui sont requis lorsque la procédure stockée est exécutée.

- La ligne commençant par `@params` définit toutes les variables utilisées par le code R et les types de données SQL correspondants.

- Les lignes qui suivent immédiatement mappent les noms de paramètres SQL aux noms de variables R correspondants.

Maintenant que vous avez inclus la fonction R dans une procédure stockée, vous pouvez facilement appeler la fonction et passer des valeurs différentes, comme suit :

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Utiliser des fonctions utilitaires R pour le dépannage

Le `utils` package, installé par défaut, fournit une variété de fonctions utilitaires pour examiner l’environnement R actuel. Ces fonctions peuvent être utiles si vous constatez des différences dans la manière dont votre code R effectue dans SQL et dans les environnements extérieurs. Par exemple, vous pouvez utiliser le R `memory.limit()` fonction pour obtenir la mémoire de l’environnement R actuel.

Étant donné que le `utils` package est installé mais pas chargé par défaut, vous devez utiliser le `library()` fonction permettant de charger en premier lieu.

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
> Beaucoup d’utilisateurs souhaitent utiliser les fonctions de minutage système dans R, tel que `system.time` et `proc.time`, pour capturer l’heure utilisée par les processus R et analyser les problèmes de performances.
