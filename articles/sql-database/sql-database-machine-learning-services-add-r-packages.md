---
title: Ajouter un package R à Machine Learning Services (préversion)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Cet article explique comment installer un package R qui n’est pas déjà installé dans Azure SQL Database Machine Learning Services (préversion).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ab066609bff773ceacb06be604e386eed5cdf7ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453334"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Ajouter un package R à Azure SQL Database Machine Learning Services (préversion)

Cet article explique comment ajouter un package R à Azure SQL Database Machine Learning Services (préversion).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Conditions préalables requises

- Installez [R](https://www.r-project.org) et [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) sur votre ordinateur local. R est disponible pour Windows, MacOS et Linux. Cet article suppose que vous utilisez Windows.

- Cet article inclut un exemple d’utilisation d’[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) pour exécuter un script R dans Azure SQL Database. Vous pouvez exécuter des scripts R à l’aide d’autres outils de gestion de base de données ou de requête, mais cet exemple suppose l’utilisation d’Azure Data Studio ou de SSMS.
   
> [!NOTE]
> Vous ne pouvez pas installer de package en exécutant un script R à l’aide de **sp_execute_external_script** dans Azure Data Studio ou SSMS. Vous pouvez uniquement installer et supprimer des packages à l’aide de la ligne de commande R et de RStudio comme décrit dans cet article. Une fois le package installé, vous pouvez accéder à ses fonctions dans un script R à l’aide de **sp_execute_external_script**.

## <a name="list-r-packages"></a>Lister les packages R

Microsoft fournit un nombre de packages R préinstallés avec Machine Learning Services dans votre base de données Azure SQL.
Vous pouvez voir la liste des packages R installés en exécutant la commande suivante dans Azure Data Studio ou SSMS.

1. Ouvrez Azure Data Studio ou SSMS et connectez-vous à votre base de données SQL Azure.

1. Exécutez la commande suivante :

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

Le résultat doit être semblable à ce qui suit.

**Résultats**

![Packages installés dans R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Ajouter un package avec sqlmlutils

Si vous devez utiliser un package qui n’est pas déjà installé dans votre base de données SQL Azure, vous pouvez l’installer à l’aide de [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** est un package conçu pour aider les utilisateurs à interagir avec les bases de données SQL (SQL Server et Azure SQL Database) et à exécuter du code R ou Python en SQL à partir d’un client R ou Python. Seule la version R de **sqlmlutils** est prise en charge dans Azure SQL Database.

Dans l’exemple suivant, vous allez installer le package **[glue](https://cran.r-project.org/web/packages/glue/)** qui peut mettre en forme et interpoler des chaînes. Ces étapes permettent d’installer **sqlmlutils** et **RODBCext** (un prérequis pour **sqlmlutils**), puis d’ajouter le package **glue**.

### <a name="install-sqlmlutils"></a>Installer **sqlmlutils**

1. Téléchargez le dernier fichier zip **sqlmlutils** à partir de https://github.com/Microsoft/sqlmlutils/tree/master/R/dist sur votre ordinateur local. Vous n’avez pas besoin de décompresser le fichier.

1. Ouvrez une **invite de commandes** et exécutez les commandes suivantes pour installer **RODBCext** et **sqlmlutils** sur votre ordinateur local. Substituez le chemin complet du fichier zip **sqlmlutils** que vous avez téléchargé (dans l’exemple, le fichier est censé se trouver dans votre dossier Documents).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Le résultat devrait être similaire à ce qui suit.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Si l’erreur « R n’est pas reconnu comme une commande interne ou externe, un programme exécutable ou un fichier de commandes » apparaît, cela signifie probablement que le chemin du fichier R.exe n’est pas inclus dans votre variable d’environnement **PATH** sur Windows. Vous pouvez ajouter le chemin à la variable d’environnement ou accéder au dossier à partir de l’invite de commandes (par exemple `cd C:\Program Files\R\R-3.5.3\bin`), puis réessayer la commande.

### <a name="add-the-package"></a>Ajouter le package

1. Ouvrez RStudio et créez un fichier de **script R**. 

1. Utilisez le code R suivant pour installer le package **glue** à l’aide de **sqlmlutils**. Substituez vos propres informations de connexion à la base de données Azure SQL.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > La valeur **scope** (étendue) peut être définie sur **PUBLIC** ou **PRIVATE**. L’étendue publique est utile pour que l’administrateur de base de données puisse installer des packages que tous les utilisateurs peuvent utiliser. L’étendue privée rend le package disponible uniquement pour l’utilisateur qui l’installe. Si vous ne spécifiez pas l’étendue, l’étendue par défaut est définie sur **PRIVÉE**.

### <a name="verify-the-package"></a>Vérifier le package

Vérifiez que le package **glue** a été installé en exécutant le script R suivant dans RStudio. Utilisez la **connexion** que vous avez définie à l’étape précédente.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Résultats**

![Contenu de la table RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Utiliser le package

Une fois le package installé, vous pouvez l’utiliser dans un script R par le biais de **sp_execute_external_script**.

1. Ouvrez Azure Data Studio ou SSMS et connectez-vous à votre base de données SQL Azure.

1. Exécutez la commande suivante :

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    Vous verrez le résultat suivant sous l’onglet **Messages**.

    **Résultats**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Supprimer le package

Si vous souhaitez supprimer le package, exécutez le script R suivant dans RStudio. Utilisez la **connexion** que vous avez définie plus haut.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Une autre façon d’installer un package R dans votre base de données Azure SQL consiste à charger le package R à partir d’un flux d’octets à l’aide de l’instruction T-SQL **CREATE EXTERNAL LIBRARY**. Consultez [Créer une bibliothèque à partir d’un flux d’octets](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) dans la documentation de référence de [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Machine Learning Services avec R dans Azure SQL Database (préversion), consultez les articles suivants.

- [Machine Learning Services avec R dans Azure SQL Database (préversion)](sql-database-machine-learning-services-overview.md)
- [Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)](sql-database-machine-learning-services-functions.md)
- [Exploiter des données R et SQL dans Machine Learning Services d’Azure SQL Database (préversion)](sql-database-machine-learning-services-data-issues.md)