---
title: Travailler avec les objets et les types de données R et SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Découvrez comment travailler avec les types de données et les objets de données dans R avec la base de données SQL Azure à l’aide des Services Machine Learning (version préliminaire), y compris les problèmes courants que vous pouvez rencontrer.
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
ms.openlocfilehash: 069a2a5b3b26bf517b57034f05ab7080ab392319
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702518"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Travailler avec des données R et SQL dans Azure SQL Database Machine Learning Services (version préliminaire)

Cet article aborde certains des problèmes courants que vous pouvez rencontrer lors du déplacement des données entre R et SQL Database dans [Machine Learning Services (avec R) dans la base de données SQL Azure](sql-database-machine-learning-services-overview.md). L’expérience que vous bénéficiez via cet exercice fournit des informations essentielles lorsque vous travaillez avec des données dans votre propre script.

Problèmes courants que vous pouvez rencontrer sont les suivantes :

- Parfois, les types de données ne correspondent
- Les conversions implicites peuvent avoir lieu
- Opérations cast et convert sont parfois nécessaires
- R et SQL utilisent différents objets de données

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Conditions préalables

- Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

- Pour exécuter l’exemple de code dans ces exercices, vous devez tout d’abord disposer d’une base de données Azure SQL avec Machine Learning Services (y compris R) activé. Pendant la préversion publique, Microsoft vous intégrera et activera le machine learning sur votre base de données nouvelle ou existante. Suivez les étapes pour vous [inscrire à la préversion](sql-database-machine-learning-services-overview.md#signup).

- Vérifiez que vous avez installé la dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Vous pouvez exécuter des scripts R à l’aide d’autres outils de gestion de base de données ou de requête, mais dans ce démarrage rapide, vous allez utiliser SSMS.

## <a name="working-with-a-data-frame"></a>Travaillez sur une trame de données

Lorsque votre script retourne les résultats à partir de R pour SQL, elle doit retourner les données comme un **data.frame**. N’importe quel autre type d’objet que vous générez dans votre script - que ce soit par une liste, facteur, vecteur ou des données binaires - doit être converti en une trame de données si vous souhaitez retourner la sortie en tant que partie des résultats de la procédure stockée. Heureusement, il existe plusieurs fonctions R pour prendre en charge la modification des autres objets en une trame de données. Vous pouvez même sérialiser un modèle binaire et retourner dans une trame de données, vous ferez plus tard dans cet article.

Tout d’abord, nous allons faire des essais avec certains objets R de base - vecteurs, matrices et listes - et voir comment la conversion en une trame de données change la sortie passée à SQL.

Comparer ces deux scripts de « Hello World » dans R. Les scripts se ressemblent beaucoup, mais la première renvoie une seule colonne de trois valeurs, tandis que la seconde retourne trois colonnes avec une seule valeur chacune.

**Exemple 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Exemple 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Pourquoi les résultats sont-ils si différents ?

La réponse peut généralement être trouvée à l’aide de R `str()` commande. Ajoutez la fonction `str(object_name)` n’importe où dans votre script R pour que les données le schéma de l’objet R spécifié retourné en tant qu’un message d’information. Vous pouvez afficher les messages dans la **Messages** onglet dans SSMS.

Pour déterminer pourquoi exemple 1 et 2 ont des résultats très différents, insérez la ligne `str(OutputDataSet)` à la fin de la `@script` définition de variable dans chaque instruction, comme ceci :

**Exemple 1 avec la fonction str ajoutée**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exemple 2 avec la fonction str ajoutée**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Maintenant, passez en revue le texte dans **Messages** pour voir pourquoi la sortie est différente.

**Résultats de l’exemple 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Résultats de l’exemple 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Comme vous pouvez le voir, un léger changement dans la syntaxe de R a un effet considérable sur le schéma des résultats. Pour tous les détails, les différences dans les types de données R sont expliquées en détail dans le *des Structures de données* section [« Advanced R » par Hadley Wickham](http://adv-r.had.co.nz).

Pour le moment, seulement à l’esprit que vous devez vérifier les résultats attendus quand vous convertissez des objets R en trames de données.

> [!TIP]
> Vous pouvez également utiliser des fonctions d’identité R, tel que `is.matrix`, `is.vector`, pour retourner des informations sur la structure de données interne.

## <a name="implicit-conversion-of-data-objects"></a>Conversion implicite des objets de données

Chaque objet de données R a ses propres règles pour la gestion des valeurs lorsqu’elles sont combinées avec d’autres objets de données si les deux objets de données ont le même nombre de dimensions, ou si un objet de données contient des types de données hétérogènes.

Par exemple, supposons que vous souhaitez effectuer une multiplication de matrice à l’aide de R. Vous voulez multiplier une matrice à colonne unique avec les valeurs par un tableau avec quatre valeurs et attendent une matrice 4 x 3 en conséquence.

Tout d’abord, créez une petite table de données de test.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Exécutez maintenant le script suivant.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

En coulisses, la colonne de trois valeurs est convertie en une seule colonne de matrice. Car une matrice est simplement un cas spécial d’un tableau dans R, le tableau `y` variable est implicitement converti en une matrice à colonne unique pour que les deux arguments conformes.

**Résultats**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1 200|1300|1400|1 500|

Notez, cependant, que se passe-t-il lorsque vous modifiez la taille du tableau `y`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Maintenant, R retourne une valeur unique comme résultat.

**Résultats**
    
|Col1|
|---|
|1542|

Pourquoi ? Dans ce cas, étant donné que les deux arguments peuvent être gérés comme des vecteurs de la même longueur, R retourne le produit interne sous forme de matrice.  Il s’agit du comportement attendu selon les règles de l’algèbre linéaire. Toutefois, cela peut poser problème si votre application en aval attend le schéma de sortie ne changent jamais !

## <a name="merge-or-multiply-columns-of-different-length"></a>Fusionner ou multiplier des colonnes de longueur différente

R offre une grande souplesse pour l’utilisation des vecteurs de tailles différentes et la combinaison de ces structures de colonnes en trames de données. Les listes de vecteurs peuvent se présenter comme un tableau, mais ils ne suivent pas toutes les règles qui régissent les tables de base de données.

Par exemple, le script suivant définit un tableau numérique de longueur 6 et le stocke dans la variable R `df1`. Le tableau numérique est ensuite combiné aux entiers de la table RTestData (créé ci-dessus) qui contient les trois (3) valeurs, à créer une trame de données, `df2`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Pour remplir la trame de données, R répète les éléments récupérés à partir de RTestData autant de fois que nécessaire pour correspondre au nombre d’éléments dans le tableau `df1`.

**Résultats**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5.|
|100|6.|

N’oubliez pas qu’une trame de données uniquement ressemble à une table, mais est en fait une liste des vecteurs.

## <a name="cast-or-convert-sql-data"></a>Cast ou convert de données SQL

R et SQL n’utilisent pas les mêmes types de données, donc lorsque vous exécutez une requête SQL pour obtenir des données et l’intégrer au runtime R, un type de conversion implicite a généralement lieu. Un autre ensemble de conversions a lieu lorsque vous retournez les données à partir de R pour SQL.

- SQL envoie les données à partir de la requête au processus R et le convertit en une représentation interne pour une plus grande efficacité.
- Le runtime R charge les données dans une variable data.frame et effectue ses propres opérations sur les données.
- Le moteur de base de données retourne les données à SQL à l’aide d’une connexion sécurisée interne et présente les données en termes de types de données SQL.
- Vous obtenez les données en vous connectant à SQL à l’aide d’une bibliothèque de client ou le réseau qui peut émettre des requêtes SQL et gérer des jeux de données tabulaires. Cette application cliente peut affecter potentiellement les données par d’autres moyens.

Pour voir comment cela fonctionne, exécutez une requête telle que celle-ci le [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) entrepôt de données. Cette vue retourne des données de ventes utilisées pour créer des prévisions.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Vous pouvez utiliser n’importe quelle version d’AdventureWorks, ou créer une autre requête à l’aide d’une base de données de votre choix. Le point consiste à essayer de manipuler des données qui contient le texte, date/heure et des valeurs numériques.

Maintenant, essayez d’utiliser cette requête comme entrée à la procédure stockée.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Si vous obtenez une erreur, vous devrez probablement apporter certaines modifications au texte de requête. Par exemple, le prédicat de chaîne dans la clause WHERE doit être placé entre deux ensembles de guillemets simples.

Après avoir obtenu la requête réussit, passez en revue les résultats de la `str` (fonction) pour voir de quelle manière R traite les données d’entrée.

**Résultats**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- La colonne datetime a été traitée à l’aide du type de données R, **POSIXct**.
- La colonne de texte « ProductSeries » a été identifié comme un **facteur**, ce qui signifie qu’une variable catégorielle. Par défaut, les valeurs de chaîne sont gérées comme des facteurs. Si vous passez une chaîne à R, il est converti en un entier à un usage interne et ensuite mappé à la chaîne de sortie.

## <a name="summary"></a>Résumé

Dans ces exemples courts, vous pouvez voir la nécessité de vérifier les effets de la conversion de données lors de la transmission SQL interroge en tant qu’entrée. Étant donné que certains types de données SQL ne sont pas pris en charge par R, tenez compte des manières suivantes pour éviter les erreurs :

- Tester vos données à l’avance et vérifiez les colonnes ou valeurs dans votre schéma peut être un problème quand il est passé au code R.
- Spécifiez les colonnes dans votre source de données d’entrée individuellement, au lieu d’utiliser `SELECT *`et connaître les modalités de chaque colonne.
- Effectuer des conversions explicites en fonction des besoins lors de la préparation de vos données d’entrée, afin d’éviter les mauvaises surprises.
- Évitez les colonnes de transmission de données (par exemple, le GUID ou rowguid) provoquent des erreurs et ne sont pas utiles pour la modélisation.

Pour plus d’informations sur les types de données de R pris en charge et non pris en charge, consultez [R bibliothèques et types de données](/sql/advanced-analytics/r/r-libraries-and-data-types.md).
