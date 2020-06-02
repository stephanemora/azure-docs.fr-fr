---
title: 'Exécuter un script R : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Exécuter un script R dans Azure Machine Learning pour exécuter du code R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: 7b72d83740e0e2b02ef9d2ea3cd1cbf04a4c99cc
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983565"
---
# <a name="execute-r-script"></a>Exécuter un script R

Cet article explique comment utiliser le module **Exécuter un script R** pour exécuter du code R dans votre pipeline de concepteur Azure Machine Learning (préversion).

R vous permet d’exécuter des tâches qui ne sont actuellement pas prises en charge par les modules existants, telles que : 
- Créer des transformations de données personnalisées
- Utiliser vos propres métriques pour évaluer des prédictions
- Générer des modèles à l’aide d’algorithmes qui ne sont pas implémentés en tant que modules autonomes dans le concepteur

## <a name="r-version-support"></a>Prise en charge de la version de R

Le concepteur Azure Machine Learning utilise la distribution CRAN (Comprehensive R Archive Network) de R. La version actuellement utilisée est CRAN 3.5.1.

## <a name="supported-r-packages"></a>Packages R pris en charge

L’environnement R est préinstallé avec plus de 100 packages. Pour obtenir la liste complète, consultez la section [Packages R préinstallés](#pre-installed-r-packages).

Vous pouvez également ajouter le code suivant à tout module **Exécuter un script R** pour afficher les packages installés.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Si votre pipeline contient plusieurs modules Execute R Script et que vous avez besoin de packages qui ne figurent pas dans la liste préinstallée, installez les packages dans chaque module, respectivement. 

## <a name="installing-r-packages"></a>Installation des packages R
Pour installer des packages R supplémentaires, utilisez la méthode `install.packages()`. Les packages sont installés pour chaque module **Exécuter un script R** et ne sont pas partagés entre d’autres modules **Exécuter un script R** .

> [!NOTE]
> Spécifiez le référentiel CRAN lors de l’installation de packages, par exemple : `install.packages("zoo",repos = "http://cran.us.r-project.org")`.

Cet exemple montre comment installer Zoo :
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
  > Vérifiez si le package existe déjà avant de l’installer, afin d’éviter de répéter l’installation. Comme `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` dans l’exemple de code ci-dessus. Répéter l’installation peut entraîner l’expiration des requêtes de service web.     

## <a name="upload-files"></a>Charger des fichiers
Le module **Exécuter un script R** prend en charge le chargement de fichiers à l'aide du kit de développement logiciel (SDK) Azure Machine Learning R.

L'exemple suivant montre comment charger un fichier image dans le module **Exécuter un script R** :
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Une fois l’exécution du pipeline terminée, vous pouvez afficher un aperçu de l’image dans le panneau de droite du module

> [!div class="mx-imgBorder"]
> ![Image chargée](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Comment configurer le module Exécuter un script R

Le module **Exécuter un script R** contient un exemple de code que vous pouvez utiliser comme point de départ. Pour configurer le module **Exécuter un script R**, indiquez un ensemble d’entrées et du code à exécuter.

![R-module](media/module/execute-r-script.png)

Les jeux de données stockés dans le concepteur sont automatiquement convertis en trame de données R quand ils sont chargés avec ce module.

1.  Ajoutez le module **Exécuter un script R** à votre pipeline.

  

1. Connectez toutes les entrées requises par le script. Les entrées sont facultatives et peuvent inclure des données ainsi que du code R supplémentaire.

    * **Dataset1** : Référencez la première entrée en tant que `dataframe1`. Le jeu de données d’entrée doit être au format CSV, TSV, ARFF, ou vous pouvez connecter un jeu de données Azure Machine Learning.

    * **Dataset2** : Référencez la deuxième entrée en tant que `dataframe2`. Ce jeu de données doit également se présenter sous la forme d’un fichier CSV, TSV, ARFF, ou d’un jeu de données Azure Machine Learning.

    * **ScriptBundle** : La troisième entrée accepte des fichiers ZIP. Le fichier compressé peut contenir plusieurs fichiers et plusieurs types de fichiers.

1. Dans la zone de texte **Script R**, tapez ou collez un script R valide.

    > [!NOTE]
    > Soyez très prudent lorsque vous écrivez votre script et assurez-vous qu’il n’existe pas d’erreur de syntaxe, telle que l’utilisation de variables non déclarées ou de fonctions ou de modules non importés. Faites également attention à la liste des packages préinstallés à la fin de ce document. Pour utiliser des packages qui ne sont pas répertoriés, installez-les dans votre script, par exemple : `install.packages("zoo",repos = "http://cran.us.r-project.org")`.
    
    > [!NOTE]
    > Les fonctions dépendantes de la bibliothèque X11 comme « Affichage » ne sont pas prises en charge, car la bibliothèque X11 n’est pas préinstallée.
    
    Pour vous aider à commencer, la zone de texte **Script R** est préremplie avec un exemple de code, que vous pouvez modifier ou remplacer.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # Please note that functions dependant on X11 library
    # such as "View" are not supported because X11 library
    # is not pre-installed.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a zip file is connected to the third input port, it is
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * Le script doit contenir une fonction nommée `azureml_main` qui est le point d’entrée pour ce module.

 * La fonction de point d’entrée doit avoir deux arguments d’entrée : `Param<dataframe1>` et `Param<dataframe2>`, même lorsque ces deux arguments ne sont pas utilisés dans la fonction.

   > [!NOTE]
    > Les données passées au module **Exécuter le script R** sont référencées en tant que `dataframe1` et `dataframe2`, ce qui est différent du concepteur Azure Machine Learning (référence de concepteur en tant que `dataset1`, `dataset2`). Vérifiez que les données d’entrée sont référencées correctement dans votre script.  
 
    > [!NOTE]
    >  Vous devrez peut-être apporter des modifications mineures au code R existant pour qu’il s’exécute dans un pipeline de concepteur. Par exemple, les données d’entrée que vous fournissez au format CSV doivent être explicitement converties en un jeu de données avant de pouvoir les utiliser dans votre code. Les types de données et de colonnes utilisés dans le langage R diffèrent également à certains égards des types de données et de colonnes utilisés dans le concepteur.

1.  **Valeur de départ aléatoire** : Tapez une valeur à utiliser dans l’environnement R en tant que valeur de départ aléatoire. Ce paramètre revient à appeler `set.seed(value)` dans le code R.  

1. Envoyez le pipeline.  

## <a name="results"></a>Résultats

Les modules **Exécuter un script R** peuvent retourner plusieurs sorties, mais elles doivent être fournies sous forme de trames de données R. Les trames de données sont automatiquement converties en jeux de données dans le concepteur pour assurer la compatibilité avec d’autres modules.

Les erreurs et messages standard de R sont retournés dans le journal du module.

S'il vous faut imprimer les résultats dans le script R, les résultats imprimés sont disponible dans **70_driver_log** sous l'onglet **Sorties + journaux** du panneau droit du module.

## <a name="sample-scripts"></a>Exemples de scripts

Il existe de nombreuses façons d’étendre votre pipeline à l’aide d’un script R personnalisé.  Cette section fournit un exemple de code pour les tâches courantes.


### <a name="add-r-script-as-an-input"></a>Ajouter un script R en tant qu’entrée

Le module **Exécuter un script R** prend en charge des fichiers de script R arbitraires en tant qu’entrées. Pour ce faire, ils doivent être chargés sur votre espace de travail en tant que partie du fichier ZIP.

1. Pour charger un fichier ZIP contenant du code R sur votre espace de travail, accédez à la page de ressource **Jeux de données**, cliquez sur **Créer un jeu de données**, puis sélectionnez **Depuis un fichier local** et l’option de type de jeu de données **Fichier**.  

1. Vérifiez que le fichier compressé est disponible dans la liste **Mes jeux de données** sous la catégorie **Jeux de données** dans l’arborescence du module de gauche.

1.  Connectez le jeu de données au port d’entrée **ScriptBundle**.

1. Tous les fichiers qui sont contenus dans le fichier ZIP sont disponibles pendant la durée d’exécution du pipeline. 

    Si le fichier de script groupé est contenu dans une structure de répertoires, la structure est conservée. Toutefois, vous devez modifier votre code pour ajouter le répertoire **./ScriptBundle** au chemin.

### <a name="process-data"></a>Traitement des données

L’exemple suivant montre comment mettre à l’échelle et normaliser les données d’entrée :

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Lire un fichier ZIP en tant qu’entrée

Cet exemple montre comment utiliser un jeu de données dans un fichier ZIP en tant qu’entrée pour le module **Exécuter un script R**.

1. Créez le fichier de données au format CSV et nommez-le « mydatafile.csv ».
1. Créez un fichier ZIP et ajoutez le fichier CSV à l’archive.
1. Chargez le fichier compressé sur votre espace de travail Azure Machine Learning. 
1. Connectez le jeu de données obtenu à l’entrée **ScriptBundle** de votre module **Exécuter un script R**.
1. Utilisez le code suivant pour lire les données CSV du fichier compressé.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Répliquer des lignes

Cet exemple montre comment répliquer des enregistrements positifs dans un jeu de données pour équilibrer l’échantillon :

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passer des objets R entre des modules Exécuter un script R

Vous pouvez passer des objets R entre des instances du module **Exécuter un script R** en utilisant le mécanisme de sérialisation interne. Cet exemple suppose que vous souhaitez déplacer l’objet R nommé `A` entre deux modules **Exécuter un script R**.

1. Ajoutez le premier module **Exécuter un script R** à votre pipeline et entrez le code suivant dans la zone de texte **Script R** pour créer un objet sérialisé `A` comme colonne dans la table de données de sortie du module :  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    La conversion explicite en type entier est effectuée, car la fonction de sérialisation génère des données au format R `Raw`, qui n’est pas pris en charge par le concepteur.

1. Ajoutez une deuxième instance du module **Exécuter un script R** et connectez-la au port de sortie du module précédent.

1. Tapez le code suivant dans la zone de texte **Script R** pour extraire l’objet `A` de la table de données d’entrée. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Packages R préinstallés

Liste actuelle de packages R préinstallés pouvant être utilisés :

|              |            | 
|--------------|------------| 
| Package      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| jeux de données     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generics     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| graphics     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0,13       | 
| outils        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
