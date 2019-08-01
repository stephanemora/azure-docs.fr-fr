---
title: 'Transformations : SDK Python de préparation des données'
titleSuffix: Azure Machine Learning service
description: Apprenez-en davantage sur la transformation et le nettoyage des données avec le SDK de préparation des données Azure Machine Learning. Utilisez des méthodes de transformation pour ajouter des colonnes, filtrer les lignes ou les colonnes indésirables, et imputer des valeurs manquantes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: add5584ccf3d9d6837e328bbf70d71598e5c0839
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694305"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformer des données avec le SDK de préparation des données Azure Machine Learning

Cet article vous permet d’apprendre les différentes méthodes de transformation de données à l’aide du package `azureml-dataprep`. Le package offre des fonctions qui simplifient l’ajout de colonnes, le filtrage des lignes ou des colonnes indésirables et l’imputation des valeurs manquantes. Consultez la documentation de référence complète relative au [package azureml-dataprep](https://aka.ms/data-prep-sdk).

> [!Important]
> Si vous générez une nouvelle solution, essayez [Azure Machine Learning Datasets](how-to-explore-prepare-data.md) (préversion) pour transformer vos données, créer des instantanés des données et stocker des définitions de jeux de données avec version. Datasets est la prochaine version du kit de développement logiciel (SDK) de préparation des données, offrant des fonctionnalités étendues pour la gestion des jeux de données dans des solutions d’intelligence artificielle. Si vous utilisez le package `azureml-dataprep` pour créer un dataflow avec vos transformations au lieu d’utiliser le package `azureml-datasets` pour créer un jeu de données, vous ne pourrez pas utiliser des instantanés ou des jeux de données avec version ultérieurement.

Cette procédure illustre les tâches suivantes à l’aide d’exemples :

- Ajouter une colonne à l’aide d’une expression
- [Imputer des valeurs manquantes](#impute-missing-values)
- [Dériver une colonne par un exemple](#derive-column-by-example)
- [Filtering](#filtering)
- [Transformations Python personnalisées](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Ajouter une colonne à l’aide d’une expression

Le SDK de préparation des données Azure Machine Learning inclut des expressions `substring` que vous pouvez utiliser pour calculer une valeur à partir de colonnes existantes, puis placer cette valeur dans une nouvelle colonne. Dans cet exemple, vous chargez des données et essayez d’ajouter des colonnes à ces données d’entrée.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||id|Case Number|Date|Block|IUCR|Primary Type|Description|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|Latitude|Longitude|Location|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


Utilisez l’expression `substring(start, length)` pour extraire le préfixe de la colonne Case Number et placer cette chaîne dans une nouvelle colonne : `Case Category`. Le fait de passer la variable `substring_expression` au paramètre `expression` crée une colonne calculée qui exécute l’expression sur chaque enregistrement.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||id|Case Number|Case Category|Date|Block|IUCR|Primary Type|Description|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|Latitude|Longitude|Location|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


Utilisez l’expression `substring(start)` pour extraire uniquement le nombre de la colonne Case Number et créez une colonne. Convertissez-la en type de données numérique à l’aide de la fonction `to_number()` et indiquez le nom de la colonne de type chaîne comme paramètre.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Imputer des valeurs manquantes

Le SDK peut imputer des valeurs manquantes dans des colonnes spécifiées. Dans cet exemple, vous chargez des valeurs de latitude et de longitude, puis essayez d’imputer les valeurs manquantes dans les données d’entrée.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||id|Arrest|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

Il manque des valeurs de latitude et de longitude dans le troisième enregistrement. Pour imputer ces valeurs manquantes, vous utilisez [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) pour apprendre une expression fixe. Il peut imputer les colonnes soit avec une valeur calculée `MIN`, `MAX` ou `MEAN`, soit avec une valeur `CUSTOM`. Quand `group_by_columns` est spécifié, les valeurs manquantes sont imputées par groupe avec `MIN`, `MAX`, et `MEAN` calculées par groupe.

Vérifiez la valeur `MEAN` de la colonne de la latitude en utilisant la fonction [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow). Cette fonction accepte un tableau de colonnes dans le paramètre `group_by_columns` pour spécifier le niveau d’agrégation. Le paramètre `summary_columns` accepte un appel `SummaryColumnsValue`. Cet appel de fonction spécifie le nom de colonne actuel, le nom du nouveau champ calculé et la `SummaryFunction` à exécuter.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                             summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                        summary_column_name='Latitude_MEAN',
                                                                        summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Arrest|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

La valeur `MEAN` des latitudes semble exacte ; utilisez la fonction `ImputeColumnArguments` pour l’imputer. Cette fonction accepte une chaîne `column_id` et un `ReplaceValueFunction` pour spécifier le type d’imputation. Quant à la valeur de longitude manquante, imputez-la avec 42 sur la base de connaissances externes.

Vous pouvez chaîner les étapes d’imputation dans un objet `ImputeMissingValuesBuilder`, à l’aide de la fonction de générateur `impute_missing_values()`. Le paramètre `impute_columns` accepte un tableau d’objets `ImputeColumnArguments`. Appelez la fonction `learn()` pour stocker les étapes d’imputation, puis appliquez-les à un objet de flux de données à l’aide `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                      group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||id|Arrest|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

Comme indiqué dans le résultat ci-dessus, la latitude manquante a été imputée avec la valeur `MEAN` du groupe `Arrest=='false'`. La longitude manquante a été imputée avec 42.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Dériver des colonnes par exemple

L’une des fonctionnalités les plus avancées du SDK de préparation de données Azure Machine Learning est la capacité à dériver des colonnes à l’aide d’exemples de résultats désirés. Elle vous permet de fournir un exemple au SDK, qui génère ensuite du code pour obtenir la transformation prévue.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

Supposez que vous devez joindre ce fichier à un jeu de données où la date et l’heure sont au format « Mar 10, 2018 | 2AM-4AM ».

```python
builder = dflow.builders.derive_column_by_example(
    source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(
    source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5)
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|

Le code ci-dessus crée tout d’abord un générateur pour la colonne dérivée. Vous fournissez un tableau de colonnes sources à prendre en compte (`DATE`) et un nom pour la nouvelle colonne à ajouter. Comme premier exemple, vous transmettez la deuxième ligne (index 1) et donnez une valeur attendue pour la colonne dérivée.

Pour finir, vous appelez `builder.preview(skip=30, count=5)` et vous pouvez voir la colonne dérivée à côté de la colonne source. Le format semble correct, mais vous voyez uniquement des valeurs pour la même date, « Jan 1, 2015 ».

Maintenant, transmettez le nombre de lignes que vous voulez ignorer (`skip`) à partir du haut afin de voir les lignes plus bas.

> [!NOTE]
> La fonction preview() ignore les lignes, mais ne renumérote pas l’index de sortie. Dans l’exemple ci-dessous, l’index 0 de la table correspond à l’index 30 dans le dataflow.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|4|1/2/2015 1:00|Feb 1, 2015 12AM-2AM|

On constate ici un problème avec le programme généré. En se basant uniquement sur l’exemple que vous avez fourni ci-dessus, le programme de dérivation a choisi d’analyser la date en tant que « Jour/Mois/Année », ce qui ne correspond pas à ce que vous souhaitez ici. Pour résoudre ce problème, ciblez un index d’enregistrement spécifique et fournissez un autre exemple en utilisant la fonction `add_example()` sur la variable `builder`.

```python
builder.add_example(
    source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|

À présent, les lignes gèrent correctement « 1/2/2015 » comme « Jan 2 2015 », mais si vous regardez au-delà de l’index 76 de la colonne dérivée, vous pouvez constater que les valeurs à la fin ne contiennent rien dans la colonne dérivée.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-8AM|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-8AM|
|2|1/29/2015 6:54|Aucun|
|3|1/29/2015 7:00|Aucun|
|4|1/29/2015 7:54|Aucun|

```python
builder.add_example(
    source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-8AM|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-8AM|
|2|1/29/2015 6:54|Jan 29, 2015 6AM-8AM|
|3|1/29/2015 7:00|Jan 29, 2015 6AM-8AM|
|4|1/29/2015 7:54|Jan 29, 2015 6AM-8AM|

 Pour voir une liste des exemples de dérivations actuelles, appelez `list_examples()` sur l’objet de générateur.

```python
examples = builder.list_examples()
```

| |DATE|exemple|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|


Dans certains cas si vous souhaitez supprimer des exemples incorrects, vous pouvez transmettre `example_row` à partir du DataFrame pandas ou la valeur `example_id`. Par exemple, si vous exécutez `builder.delete_example(example_id=-1)`, le premier exemple de transformation est supprimé.


Appelez `to_dataflow()` sur le générateur. Celui-ci retourne un flux de données avec les colonnes dérivées souhaitées ajoutées.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtrage

Le Kit de développement logiciel (SDK) inclut les méthodes [`drop_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) et [`filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) pour vous permettre de filtrer des lignes ou des colonnes.

### <a name="initial-setup"></a>Configuration initiale

> [!Note]
> Dans cet exemple, l’URL n’est pas une URL complète. Au lieu de cela, elle fait référence au dossier demo dans l’objet blob. L’URL complète des données est https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv.

Ce que nous faisons dans ce tutoriel, c’est charger tous les fichiers dans le dossier et d’agréger le résultat dans green_df_raw et yellow_df_raw.

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(
    path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5\.|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtrage de colonnes

Pour filtrer des colonnes, utilisez `drop_columns()`. Cette méthode prend une liste de colonnes à supprimer, ou un argument plus complexe appelé [`ColumnSelector`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py).

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrage de colonnes avec liste de chaînes

Dans cet exemple, `drop_columns()` prend une liste de chaînes. Chaque chaîne doit correspondre exactement à la colonne à supprimer.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|

#### <a name="filtering-columns-with-regex"></a>Filtrage de colonnes avec expressions régulières

Vous pouvez également utiliser l’expression `ColumnSelector` pour supprimer des colonnes qui correspondent à une expression régulière. Dans cet exemple, vous supprimez toutes les colonnes qui correspondent à l’expression `Column*|.*longitude|.*latitude`.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector(
    'Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|

## <a name="filtering-rows"></a>Filtrage de lignes

Pour filtrer des lignes, utilisez `filter()`. Cette méthode prend une expression du SDK de préparation des données Azure Machine Learning comme argument, et retourne un nouveau flux de données avec les lignes que l’expression évalue comme ayant la valeur True. Les expressions sont générées à l’aide de générateurs d’expressions (`col`, `f_not`, `f_and`, `f_or`) et d’opérateurs ordinaires (>, <>, =, <=, ==, !=).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrage de lignes avec expressions simples

Utilisez le générateur d’expressions `col` en spécifiant le nom de colonne comme argument de chaîne `col('column_name')`. Utilisez cette expression en combinaison avec les opérateurs standard suivants >, <, >=, <=, ==, != pour générer une expression comme `col('Tip_amount') > 0`. Pour finir, transmettez l’expression générée dans la fonction `filter()`.

Dans cet exemple, `dflow.filter(col('Tip_amount') > 0)` retourne un nouveau flux de données avec les lignes dans lesquelles la valeur de `Tip_amount` est supérieure à 0.

> [!NOTE] 
> `Tip_amount` est d’abord converti en type numérique, ce qui nous permet de créer une expression qui le compare à d’autres valeurs numériques.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5\.|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrage de lignes avec expressions complexes

Pour filtrer à l’aide d’expressions complexes, combinez une ou plusieurs expressions simples avec les générateurs d’expressions `f_not`, `f_and` ou `f_or`.

Dans cet exemple, `dflow.filter()` retourne un nouveau flux de données avec les lignes où `'Passenger_count'` est inférieur à 5 et `'Tolls_amount'` est supérieur à 0.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(
    dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

Il est également possible de filtrer des lignes en combinant plusieurs générateurs d’expressions pour créer une expression imbriquée.

> [!NOTE]
> `lpep_pickup_datetime` et `Lpep_dropoff_datetime` sont d’abord converties en datetime, ce qui nous permet de créer une expression la comparant à d’autres valeurs datetime.

```python
dflow = dflow.to_datetime(
    ['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013, 7, 1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8,22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Transformations Python personnalisées

Il y aura toujours des scénarios dans lesquels l’option la plus simple pour effectuer une transformation consiste à écrire son propre script. Le SDK fournit trois points d’extension que vous pouvez utiliser pour les scripts Python personnalisés.

- Nouvelle colonne de script
- Nouveau filtre de script
- Transform partition

Chaque extension est prise en charge dans le runtime de montée en puissance (scale-up) et de mise à l’échelle horizontale (scale-out). L’un des principaux avantages offerts par ces points d’extension est que vous n’avez pas besoin d’extraire toutes les données pour créer un cadre de données. Votre code Python personnalisé s’exécutera comme les autres transformations, à grande échelle, par partition et généralement en parallèle.

### <a name="initial-data-preparation"></a>Préparation initiale des données

Commencez par charger des données à partir d’objets blob Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |

Réduisez la taille du jeu de données et effectuez des transformations de base : supprimez des colonnes, remplacez des valeurs, convertissez des types, etc.

```python
dflow = dflow.keep_columns(
    ['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(
    columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1,017100e+10|Aucun|
|1|ALABAMA|Hale County|1,017100e+10|Aucun|

Recherchez les valeurs null à l’aide du filtre suivant.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1,017100e+10|Aucun|
|1|ALABAMA|Hale County|1,017100e+10|Aucun|

### <a name="transform-partition"></a>Transform partition

Utilisez [`transform_partition()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) pour remplacer toutes les valeurs Null par un 0. Ce code sera exécuté par partition, et non sur la totalité du jeu de données à la fois. Cela signifie que sur un jeu de données volumineux, ce code peut s’exécuter en parallèle pendant que le runtime traite les données, partition par partition.

Le script Python doit définir une fonction appelée `transform()` qui accepte deux arguments, `df` et `index`. L’argument `df` est un DataFrame pandas qui contient les données de la partition, tandis que l’argument `index` est un identificateur unique de la partition. La fonction de transformation peut modifier entièrement le DataFrame transmis, mais doit retourner un DataFrame. Toutes les bibliothèques que le script Python importe doivent exister dans l’environnement dans lequel le flux de données est exécuté.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1,017100e+10|0.0|
|1|ALABAMA|Hale County|1,017100e+10|0.0|

### <a name="new-script-column"></a>Nouvelle colonne de script

Vous pouvez utiliser un script Python pour créer une colonne contenant le nom du conté et le nom de l’état et également pour mettre en majuscule le nom de l’état. Pour cela, utilisez la méthode [`new_script_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) sur le flux de données.

Le script Python doit définir une fonction appelée `newvalue()` qui accepte un seul argument (`row`). L’argument `row` est un dictionnaire (`key` : nom de colonne, `val` : valeur actuelle) et est destiné à être passé à cette fonction pour chaque ligne du jeu de données. Cette fonction doit retourner une valeur à utiliser dans la nouvelle colonne. Toutes les bibliothèques que le script Python importe doivent exister dans l’environnement dans lequel le flux de données est exécuté.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale County, Alabama|1,017100e+10|0.0|
|1|ALABAMA|Hale County|Hale County, Alabama|1,017100e+10|0.0|

### <a name="new-script-filter"></a>Nouveau filtre de script

Générez une expression Python avec [`new_script_filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) pour filtrer le jeu de données uniquement sur les lignes où « Hale » ne figure pas dans la nouvelle colonne `county_state`. L’expression retourne `True` si nous souhaitons conserver la ligne, et `False` pour supprimer la ligne.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|

## <a name="next-steps"></a>Étapes suivantes

* Consultez le [didacticiel](tutorial-data-prep.md) sur le Kit de développement logiciel (SDK) de préparation des données Azure Machine Learning pour obtenir un exemple de résolution d’un scénario spécifique
