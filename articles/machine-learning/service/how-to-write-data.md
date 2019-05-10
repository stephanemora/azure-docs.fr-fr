---
title: 'Écriture : Kit de développement logiciel (SDK) Python de préparation des données'
titleSuffix: Azure Machine Learning service
description: Découvrez comment écrire des données avec le SDK de préparation des données Azure Machine Learning. Vous pouvez écrire des données à tout moment dans un flux de données, ainsi que dans les fichiers situés aux emplacements pris en charge (système de fichiers local, Stockage Blob Azure et Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 0275d27a0a27d0279886f6f7fd15b14d312a44ea
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472003"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Écrire et de configurer les données avec le SDK Azure Machine Learning Data Prep

Dans cet article, vous découvrez différentes méthodes pour écrire des données à l’aide de la [SDK Python de Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) et comment configurer ces données pour l’expérimentation avec le [Azure Machine Learning SDK pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Les données de sortie peuvent être écrites à tout moment dans un flux de données. Les écritures sont ajoutés comme suit au flux de données qui en résulte et ces étapes s’exécutent chaque fois que le flux de données s’exécute. Les données sont écrites dans plusieurs fichiers de partition pour permettre des écritures parallèles.

> [!Important]
> Si vous générez une nouvelle solution, essayez le [jeux de données Azure Machine Learning](how-to-explore-prepare-data.md) (version préliminaire) pour transformer vos données, les données d’instantané et de stocker les définitions de jeu de données avec contrôle de version. Jeux de données est la prochaine version de la préparation des données SDK, offre des fonctionnalités étendues pour la gestion des jeux de données dans les solutions d’intelligence artificielle.
> Si vous utilisez le `azureml-dataprep` package pour créer un flux de données avec vos transformations au lieu d’utiliser le `azureml-datasets` package pour créer un jeu de données, vous ne pourrez pas utiliser des instantanés ou des jeux de données avec version ultérieurement.

Dans la mesure où il n’existe pas de limitation quant au nombre d’étapes d’écriture qu’un pipeline peu contenir, vous pouvez facilement d’écrire des étapes pour obtenir des résultats intermédiaires utilisables pour la résolution des problèmes ou dans d’autres pipelines.

Chaque fois que vous exécutez une étape d’écriture, une extraction complète des données du flux de données est effectuée. Par exemple, un flux de données comportant trois étapes d’écriture lit et traite trois fois chacun des enregistrements du jeu de données.

## <a name="supported-data-types-and-location"></a>Types de données pris en charge et emplacement

Les formats de fichier suivants sont pris en charge.
-   Fichiers délimités (CSV, TSV, etc.)
-   Fichiers Parquet

Utilisez le SDK Python Azure Machine Learning Data Prep, vous pouvez écrire des données à :
+ un système de fichiers local
+ un stockage Azure Blob
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Considérations sur Spark

Lorsque vous exécutez un flux de données dans Spark, vous devez écrire les données dans un dossier vide. Une tentative pour effectuer une écriture dans un dossier existant provoque un échec. Pour éviter que l’écriture n’échoue, vérifiez que le dossier cible est vide ou utilisez un emplacement cible différent pour chaque exécution.

## <a name="monitoring-write-operations"></a>Supervision des opérations d’écriture

Pour des raisons pratiques, un fichier sentinelle nommé SUCCESS est généré une fois qu’une écriture est terminée. Sa présence vous permet de savoir quand une écriture intermédiaire est effectuée, sans devoir attendre la fin du pipeline.

## <a name="example-write-code"></a>Exemple de code d’écriture

Pour cet exemple, commencez par charger des données dans un flux de données à l’aide de `auto_read_file()`. Vous réutiliserez ces données sous différents formats.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Exemple de sortie :

| | Colonne1 | Colonne2 | Colonne3 | Colonne4 | Colonne5 | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | Aucun | NON | NON | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | Aucun | NON | NON | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | Aucun | NON | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | Aucun | NON | NON |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | Aucun | NON | NON | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Exemple de fichier délimité

Le code suivant utilise la [ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-csv-directory-path--destinationpath--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) fonction pour écrire des données dans un fichier délimité.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Exemple de sortie :

| | Colonne1 | Colonne2 | Colonne3 | Colonne4 | Colonne5 | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | ERREUR | NON | NON | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | ERREUR | NON | NON | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | ERREUR | NON | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | ERREUR | NON | NON |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | ERREUR | NON | NON | ENSO |    59783.0 | 5350.0 |  500.0|

Dans la sortie précédente, plusieurs erreurs apparaissent dans les colonnes numériques, car certains nombres n’ont pas été analysés correctement. Lorsqu’elles sont écrites au format CSV, les valeurs Null sont par défaut remplacées par la chaîne « ERROR ».

Ajoutez des paramètres à votre appel d’écriture et spécifiez la chaîne à utiliser pour représenter les valeurs Null.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Le code précédent produit cette sortie :

| | Colonne1 | Colonne2 | Colonne3 | Colonne4 | Colonne5 | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NON | NON | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NON | NON | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NON | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NON | NON |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NON | NON | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Exemple de fichier Parquet

Semblable à `write_to_csv()`, le [ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-parquet-file-path--typing-union--destinationpath--nonetype----none--directory-path--typing-union--destinationpath--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) fonction retourne un nouveau flux de données avec une écriture étape Parquet qui est exécutée lorsque le flux de données s’exécute.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Exécutez le flux de données pour lancer l’opération d’écriture.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

Le code précédent produit cette sortie :

|   | Colonne1 | Colonne2 | Colonne3 | Colonne4 | Colonne5 | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NON | NON | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NON | NON | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NON| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NON| NON| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NON| NON| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Configurer les données d’apprentissage machine automatisée

Passer de votre fichier de données qui vient d’être écrites dans un [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) objet en vue de l’apprentissage machine automatisée. 

L’exemple de code suivant illustre comment convertir votre flux de données à une trame de données Pandas et de diviser par la suite, en apprentissage et test des jeux de données pour l’apprentissage machine automatisée.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Si vous n’avez pas besoin des étapes de préparation de données intermédiaire, comme dans l’exemple précédent, vous pouvez passer votre flux de données directement dans `AutoMLConfig`.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Étapes suivantes
* Consultez le kit SDK [vue d’ensemble](https://aka.ms/data-prep-sdk) des modèles de conception et des exemples d’utilisation 
* Consultez l’apprentissage automatisé [didacticiel](tutorial-auto-train-models.md) pour obtenir un exemple de modèle de régression
