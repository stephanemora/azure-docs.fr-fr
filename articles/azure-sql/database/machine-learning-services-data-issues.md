---
title: Travailler avec des types de données et des objets R et SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Découvrez comment travailler avec des types de données et des objets de données en R avec Azure SQL Database à l’aide de Machine Learning Services (préversion), notamment les problèmes courants que vous pouvez rencontrer.
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
ROBOTS: NOINDEX
ms.openlocfilehash: f784d6ef56ad5cb800c0061fbb5d0d4ca3252fa0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84035660"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Exploiter des données R et SQL dans Machine Learning Services d’Azure SQL Database (préversion)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Cet article aborde certains des problèmes courants que vous pouvez rencontrer lors du déplacement des données entre R et SQL Database dans [Machine Learning Services (avec R) dans Azure SQL Database](machine-learning-services-overview.md). L’expérience que vous obtenez dans le cadre de cet exercice fournit une base essentielle lorsque vous travaillez avec des données dans votre propre script.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Voici quelques-uns des problèmes courants que vous pouvez rencontrer :

- Les types de données ne correspondent pas toujours
- Des conversions implicites peuvent avoir lieu
- Des opérations de transtypage et de conversion sont parfois nécessaires
- R et SQL utilisent différents objets de données

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

- Pour exécuter l'exemple de code dans ces exercices, vous devez d'abord disposer d'une instance activée d'[Azure SQL Database avec Machine Learning Services (avec R)](machine-learning-services-overview.md).

- Vérifiez que vous avez installé la dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Vous pouvez exécuter des scripts R à l’aide d’autres outils de gestion de base de données ou de requête, mais dans ce démarrage rapide, vous allez utiliser SSMS.

## <a name="working-with-a-data-frame"></a>Travail avec un data frame

Lorsque votre script retourne des résultats de R vers SQL, il doit retourner les données sous la forme **data.frame**. Tout autre type d’objet que vous générez dans votre script (liste, facteur, vecteur ou données binaires) doit être converti en trame de données si vous souhaitez retourner la sortie de l’objet dans les résultats de la procédure stockée. Heureusement, plusieurs fonctions R prennent en charge la transformation d’autres objets en data frame. Vous pouvez même sérialiser un modèle binaire et le retourner dans un data frame, comme vous le ferez plus tard dans cet article.

Commençons par faire des essais avec des objets R de base (vecteurs, matrices et listes) et voyons comment la conversion en data frame modifie la sortie passée à SQL.

Comparez ces deux scripts « Hello World » dans R. Les scripts semblent presque identiques, mais le premier retourne une seule colonne de trois valeurs, tandis que le second retourne trois colonnes avec une seule valeur chacune.

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

La réponse peut généralement être trouvée à l’aide de la commande R `str()`. Ajoutez la fonction `str(object_name)` n’importe où dans votre script R pour que le schéma de données de l’objet R spécifié soit retourné en tant que message d’information. Vous pouvez afficher les messages dans l’onglet **Messages** de SSMS.

Afin de déterminer pourquoi les résultats des exemples 1 et 2 sont si différents, insérez la ligne `str(OutputDataSet)` à la fin de la définition de la variable `@script` dans chaque instruction, comme ceci :

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

Maintenant, passez en revue le texte dans **Messages** afin de voir pourquoi la sortie est différente.

**Résultats de l’exemple 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Résultats de l’exemple 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Comme vous pouvez le voir, une légère modification de la syntaxe de R a un effet considérable sur le schéma des résultats. Pour tous les détails, les différences dans les types de données R sont expliqués en détail dans la section *Structures de données* dans [« R avancé » par Hadley Wickham](http://adv-r.had.co.nz).

Pour le moment, ayez seulement à l’esprit que vous devez vérifier les résultats attendus quand vous convertissez de force des objets R en data frames.

> [!TIP]
> Vous pouvez également utiliser des fonctions d’identité R, telles que `is.matrix`, `is.vector` pour retourner des informations sur la structure de données interne.

## <a name="implicit-conversion-of-data-objects"></a>Conversion implicite d’objets de données

Chaque objet de données R a ses propres règles de gestion des valeurs lorsqu’elles sont combinées avec d’autres objets de données si les deux objets de données ont le même nombre de dimensions, ou si un objet de données contient des types de données hétérogènes.

Par exemple, supposons que vous souhaitez effectuer une multiplication de matrice à l’aide de R. Vous voulez multiplier une matrice à colonne unique avec les trois valeurs par un tableau avec quatre valeurs, et vous attendez une matrice 4 x 3 comme résultat.

Créez une petite table de données de test.

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

En coulisses, la colonne de trois valeurs est convertie en matrice à colonne unique. Comme une matrice n’est qu’un cas de tableau spécial dans R, le tableau `y` fait implicitement l’objet d’une conversion forcée en matrice à colonne unique pour rendre les deux arguments conformes.

**Résultats**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1 300|1400|1500|

Toutefois, observez ce qui se passe quand vous modifiez la taille du tableau `y`.

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
|1 542|

Pourquoi ? Dans ce cas, les deux arguments peuvent être traités comme des vecteurs de même longueur, ce qui permet à R de retourner le produit interne sous forme de matrice.  C’est le comportement attendu selon les règles de l’algèbre linéaire. Toutefois, cela peut poser des problèmes si votre application en aval attend que le schéma de sortie ne change jamais !

## <a name="merge-or-multiply-columns-of-different-length"></a>Fusionner ou multiplier des colonnes de longueur différente

R offre une grande flexibilité dans l’utilisation de vecteurs de tailles différentes et la combinaison des structures en colonnes en trames de données. Les listes de vecteurs peuvent ressembler à un tableau, mais elles ne suivent pas toutes les règles qui régissent les tables de base de données.

Par exemple, le script suivant définit un tableau numérique de longueur 6 et le stocke dans la variable R `df1`. Le tableau numérique est ensuite combiné avec des entiers de la table RTestData (créée ci-dessus), qui contient trois (3) valeurs, pour créer un data frame, `df2`.

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

Pour remplir le data frame, R répète les éléments récupérés dans RTestData autant de fois que nécessaire pour que cela corresponde au nombre d’éléments dans le tableau `df1`.

**Résultats**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

N’oubliez pas que, même si un data frame ressemble à un tableau, il s’agit en fait une liste de vecteurs.

## <a name="cast-or-convert-sql-data"></a>Convertir en force ou convertir des données SQL

R et SQL n’utilisent pas les mêmes types de données. De ce fait, lorsque vous exécutez une requête SQL pour obtenir des données et transmettez au runtime R, un type de conversion implicite est généralement effectué. Un autre ensemble de conversions est effectué lorsque vous retournez des données de R vers SQL.

- SQL envoie les données de la requête au processus R et les convertit en représentation interne pour une plus grande efficacité.
- Le runtime R charge les données dans une variable data.frame et effectue ses propres opérations sur ces données.
- Le moteur de base de données retourne les données à SQL à l’aide d’une connexion sécurisée interne et présente les données en termes de types de données SQL.
- Vous obtenez les données en vous connectant à SQL à l’aide d’une bibliothèque de client ou réseau qui peut émettre des requêtes SQL et gérer des jeux de données tabulaires. Cette application cliente peut potentiellement affecter les données d’une autre façon.

Pour voir comment cela fonctionne, exécutez une requête telle que celle-ci sur l’entrepôt de données [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks). Cette vue retourne des données de ventes utilisées pour créer des prévisions.

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
> Vous pouvez utiliser n’importe quelle version d’AdventureWorks ou créer une requête différente à l’aide d’une de vos bases de données. Il s’agit d’essayer de gérer des données contenant un texte, DateHeure et des valeurs numériques.

Maintenant, essayez d’utiliser cette requête comme entrée pour la procédure stockée.

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

Si vous obtenez une erreur, vous devrez probablement apporter certaines modifications au texte de la requête. Par exemple, le prédicat de chaîne dans la clause WHERE doit être placé entre deux ensembles de guillemets simples.

Une fois la requête réussie, passez en revue les résultats de la fonction `str` pour voir comment R traite les données d’entrée.

**Résultats**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- La colonne DateHeure a été traitée à l’aide du type de données R, **POSIXct**.
- La colonne texte « ProductSeries » a été identifiée comme **facteur**, autrement dit une variable de catégorie. Par défaut, les valeurs de chaîne sont gérées comme des facteurs. Si vous passez une chaîne à R, elle est convertie en entier pour un usage interne, puis mappée à la chaîne à la sortie.

## <a name="summary"></a>Résumé

À partir de ces exemples courts, vous pouvez voir la nécessité de vérifier les effets de la conversion de données lors du passage de requêtes SQL en tant qu’entrée. Étant donné que certains types de données SQL ne sont pas pris en charge par R, envisagez de procéder comme suit pour éviter les erreurs :

- Testez vos données au préalable. Vérifiez les colonnes ou valeurs dans votre schéma qui peuvent poser problème quand elles sont passées au code R.
- Spécifiez individuellement des colonnes dans votre source de données d’entrée plutôt que d’utiliser `SELECT *`, et sachez comment chaque colonne sera traitée.
- Effectuez des conversions forcées explicites en fonction des besoins lors de la préparation de vos données d’entrée afin d’éviter les mauvaises surprises.
- Évitez de passer des colonnes de données (telles que des GUID ou des rowguid) qui provoquent des erreurs et qui ne sont pas utiles pour la modélisation.

Pour plus d’informations sur les types de données R pris en charge et non pris en charge, consultez [Bibliothèques R et types de données](/sql/advanced-analytics/r/r-libraries-and-data-types).
