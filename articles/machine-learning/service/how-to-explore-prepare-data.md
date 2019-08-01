---
title: Explorer et transformer des données (classe Dataset)
titleSuffix: Azure Machine Learning service
description: Explorer les données à l'aide de résumés de statistiques et préparer les données grâce au nettoyage des données, à la transformation et à l'ingénierie des caractéristiques
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: f680a1cb15edf0141897c74da3b7c7afa01acae0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699116"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Explorer et préparer les données avec la classe Dataset (préversion)

Découvrez comment explorer et préparer des données avec le paquet azureml-datasets dans le [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). La classe [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) (préversion) vous permet d'explorer et de préparer vos données en fournissant des fonctions telles que l’échantillonnage, les résumés de statistiques et les transformations intelligentes. Les étapes de transformation sont sauvegardées dans des [définitions Dataset](how-to-manage-dataset-definitions.md), avec la possibilité de gérer plusieurs fichiers volumineux de différents schémas et de manière hautement évolutive.

> [!Important]
> Certaines classes Dataset (préversion) comportent des dépendances sur le paquet [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). Même si les fonctions de transformation peuvent être appliquées directement avec les [fonctions de préparation des données](how-to-transform-data.md) en disponibilité générale, nous recommandons d’utiliser les wrappers de paquets Dataset décrits dans cet article si vous créez une solution. Azure Machine Learning Datasets (préversion) vous permet non seulement de transformer vos données, mais également de [créer des instantanés des données](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) et de stocker des [définitions de jeux de données versionnées](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Datasets est la prochaine version du SDK de préparation des données, offrant des fonctionnalités étendues pour la gestion des jeux de données dans des solutions d’intelligence artificielle.

## <a name="prerequisites"></a>Prérequis

Pour explorer et préparer vos données, vous aurez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Un espace de travail de service Microsoft Azure Machine Learning. Consultez [Créer un espace de travail pour Azure Machine Learning service](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Le SDK Azure Machine Learning pour Python (version 1.0.21 ou ultérieure), qui inclut le paquet azureml-datasets. Pour installer ou mettre à jour la dernière version du SDK, consultez [Installer ou mettre à jour le SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning. Pour installer ou mettre à jour la dernière version du SDK, consultez [Installer ou mettre à jour le SDK de préparation des données](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Téléchargez les fichiers qui accompagnent les exemples : [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) et [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>échantillonnage

Prenez un échantillon de vos données pour obtenir un premier aperçu de l’architecture et du contenu de vos données. À l'heure actuelle, la méthode [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) de la classe Dataset prend en charge les stratégies d'échantillonnage N premiers, Aléatoire simple et Stratifié.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Exemple des N premiers

Pour l'échantillonnage N premiers, les n premiers enregistrements de votre jeu de données constituent votre échantillon. Cette méthode est utile si vous essayez juste d'obtenir un aperçu de vos enregistrements de données ou d’afficher les champs qui figurent dans vos données.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRATIQUE FRAUDULEUSE|...
1|10516598|HZ258664|15/4/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
2|10519196|HZ261252|15/4/2016 10:00|104XX S SACRAMENTO AVE|1154|PRATIQUE FRAUDULEUSE|...
3|10519591|HZ261534|15/4/2016 9:00|113XX S PRAIRIE AVE|1120|PRATIQUE FRAUDULEUSE|...
4|10534446|HZ277630|15/4/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="simple-random-sample"></a>Échantillon aléatoire simple

Dans l'échantillonnage Aléatoire simple, chaque membre de la population de données a la même chance d'être sélectionné comme élément de l'échantillon. Dans la stratégie d'échantillonnage `simple_random`, les enregistrements de votre jeu de données sont sélectionnés en fonction de la probabilité spécifiée et retournent un jeu de données modifié. Le paramètre seed est facultatif.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|15/4/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10519196|HZ261252|15/4/2016 10:00|104XX S SACRAMENTO AVE|1154|PRATIQUE FRAUDULEUSE|...
2|10534446|HZ277630|15/4/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="stratified-sample"></a>Échantillon stratifié

Les échantillons stratifiés permettent de s'assurer que certains groupes d'une population sont représentés dans l'échantillon. Dans la stratégie d'échantillonnage `stratified`, la population est divisée en strates, ou sous-groupes, selon les similarités, et les enregistrements sont choisis au hasard dans chaque strate selon les pondérations des strates indiquées par le paramètre `fractions`.

Dans l'exemple suivant, nous regroupons chaque enregistrement selon les colonnes spécifiées, et incluons ledit enregistrement en fonction des informations de pondération des strates X dans `fractions`. Si aucune strate n'est spécifiée ou si l'enregistrement ne peut pas être regroupé, la pondération par défaut de l'échantillon est 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|15/4/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10534446|HZ277630|15/4/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|THEFT|...

## <a name="explore-with-summary-statistics"></a>Explorer avec un résumé des statistiques

 Détectez les anomalies, les valeurs manquantes ou le nombre d'erreurs avec la méthode [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-). Cette fonction permet d'obtenir le profil et les résumés de statistiques de vos données, ce qui vous aide à déterminer les opérations de préparation de données nécessaires à appliquer.

```Python
dataset.get_profile()
```

||Type|Min|max|Count|Manquant|Non manquant|Manquant (%)|Erreurs|Vide|Quantile 0,1 %|Quantile 1 %|Quantile 5 %|Quantile 25 %|Quantile 50 %|Quantile 75 %|Quantile 95 %|Quantile 99 %|Quantile 99,9 %|Moyenne|Écart type|Variance|Asymétrie|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Case Number|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Block|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primary Type|FieldType.STRING|PRATIQUE FRAUDULEUSE|THEFT|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Description|FieldType.STRING|VÉRIFICATION FICTIVE|PLUS DE 500 $|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Location Description|FieldType.STRING||ÉCOLE, PUBLIC, BÂTIMENT|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Arrest|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domestic|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5\.|24|10.0|0.0|10.0|0.0|0.0|0.0|5\.|5\.|5\.|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5\.|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Community Area|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI Code|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X Coordinate|FieldType.INTEGER|1.16309e+06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Y Coordinate|FieldType.INTEGER|1.8315e+06|1.908e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
Year|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Updated On|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Longitude|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Imputer des valeurs manquantes

Dans Datasets, les valeurs nulles, les valeurs NaN et les valeurs qui ne contiennent aucun contenu sont considérées comme des valeurs manquantes. Ces valeurs peuvent avoir un impact sur les performances de vos modèles Machine Learning ou conduire à des conclusions invalides. L'imputation est une approche courante pour traiter les valeurs manquantes, et elle est utile lorsque vous avez un pourcentage élevé d'enregistrements de valeurs manquantes que vous ne pouvez pas simplement supprimer.

D'après le profil Dataset généré dans la section précédente, nous constatons que les colonnes `Latitude` et `Longitude` affichent un pourcentage élevé de valeurs manquantes. Dans cet exemple, nous calculons la moyenne et imputons les valeurs manquantes pour ces deux colonnes.

Tout d'abord, obtenez la dernière définition du jeu de données avec [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) et réduisez les données avec [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow) : nous ne voyons donc que les colonnes qui nous intéressent.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Arrest| Latitude|Longitude|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

Vérifiez maintenant la valeur `MEAN` de la colonne de la latitude en utilisant la fonction [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow). Cette fonction accepte un tableau de colonnes dans le paramètre `group_by_columns` pour spécifier le niveau d’agrégation. Le paramètre `summary_columns` accepte la fonction `SummaryColumnsValue`, qui spécifie le nom de colonne actuel, le nom du nouveau champ calculé et la fonction `SummaryFunction` à exécuter.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Arrest|Latitude_MEAN|
--|-----|--------|
|0|False|41.780049|

Une fois les valeurs à imputer vérifiées, utilisez [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) pour apprendre une expression fixe qui impacte les colonnes avec une valeur calculée `MIN`, `MAX`, `MEAN` ou `CUSTOM`. Quand `group_by_columns` est spécifié, les valeurs manquantes sont imputées par groupe avec `MIN`, `MAX`, et `MEAN` calculées par groupe.

La fonction [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) accepte une chaîne column_id string, et une fonction `ReplaceValueFunction` pour spécifier le type d’imputation. Quant à la valeur de longitude manquante, imputez-la avec 87 sur la base de connaissances externes.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Vous pouvez chaîner les étapes d’imputation dans un objet `ImputeMissingValuesBuilder` en utilisant la [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) fonction de classe [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). Le paramètre `impute_columns` accepte un tableau d’objets `ImputeColumnArguments`.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Appelez la fonction [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) pour stocker les étapes d’imputation, puis appliquez-les à un objet de flux de données à l’aide de [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Comme indiqué dans la table de sortie suivante, la latitude manquante a été imputée avec la valeur `MEAN` du groupe `Arrest==False`, et la valeur -87 a été imputée à la longitude manquante.

||id|Arrest|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

Mettez à jour la définition du jeu de données avec [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) pour conserver les étapes de transformation effectuées.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Arrest|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Créer des règles d’assertion

Souvent, les données que nous utilisons lors du nettoyage et de la préparation des données ne représentent qu'un sous-ensemble de l'ensemble du total des données dont nous avons besoin pour la production. Par conséquent, certaines des hypothèses que nous émettons dans le cadre de notre nettoyage pourraient se révéler fausses. Par exemple, dans un jeu de données continuellement mis à jour, une colonne qui ne contenait à l'origine que des nombres d'une certaine plage peut contenir une plage de valeurs plus étendue dans les exécutions ultérieures. Ces erreurs entraînent souvent des ruptures de pipelines ou des données incorrectes.

Les jeux de données permettent de créer des assertions sur les données, évaluées au fur et à mesure que le pipeline s'exécute. Ces assertions nous permettent de vérifier que nos hypothèses sur les données demeurent exactes et, dans le cas contraire, de traiter les défaillances en conséquence.

Par exemple, si vous voulez limiter les valeurs `Latitude` et `Longitude` de votre jeu de données à des plages numériques spécifiques, la méthode [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) garantit que c'est toujours le cas.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Min|max|Count|Manquant|Non manquant|Manquant (%)|Erreurs|Vide|Quantile 0,1 %|Quantile 1 %|Quantile 5 %|Quantile 25 %|Quantile 50 %|Quantile 75 %|Quantile 95 %|Quantile 99 %|Quantile 99,9 %|Moyenne|Écart type|Variance|Asymétrie|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Arrest|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
Longitude|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

À partir du profil, vous voyez que la valeur `Error Count` de la colonne `Longitude` est 3. Le code suivant filtre le jeu de données, récupère l'erreur et identifie la valeur provoque l'échec de l'assertion. De là, ajustez votre code et nettoyez vos données de manière appropriée.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Dériver des colonnes par exemple

L’une des fonctionnalités les plus avancées pour les jeux de données est la capacité à dériver des colonnes à l’aide d’exemples de résultats désirés. Elle vous permet de fournir un exemple au SDK, qui génère ensuite du code pour obtenir les transformations prévues.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Case Number|Date|Block|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Supposons que vous deviez transformer le format de la date et de l'heure en '2016-04-04 10PM-12AM'. Dans l'argument [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow), fournissez des exemples de la sortie souhaitée dans le paramètre `example_data`, au format suivant : *(sortie originale, sortie souhaitée)* .

Le code suivant donne deux exemples de sortie souhaitée, ("2016-04-04 23:56:00", "2016-04-04 10PM-12AM") et ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Dans le tableau suivant, notez qu'une nouvelle colonne Date_Time_Range contient des enregistrements au format spécifié.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Regroupements probables

Lorsque vous recueillez des données de différentes sources, vous pouvez rencontrer des variations dans l'orthographe, la capitalisation ou les abréviations d’entités similaires. Standardisez et rapprochez automatiquement ces variantes avec la fonctionnalité de regroupement probable du SDK, ou le clustering de texte.

Par exemple, la colonne `inspections.business.city` contient plusieurs formes du nom de ville « San Francisco ».

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-Ezee Indian Foods|3861 24th St|SF|...
1|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|...

Utilisons la méthode [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) pour ajouter une colonne avec la forme canonique automatiquement détectée de « San Francisco ». Les arguments `source_column` et `new_column_name` sont requis. Vous avez également la possibilité de :

* Créer une colonne `similarity_score_column_name` qui montre le score de similarité entre chaque paire de valeurs originales et canoniques.

* Fournir un `similarity_threshold`, qui représente le score minimum de similarité pour les valeurs à regrouper.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-Ezee Indian Foods|3861 24th St|SF|San Francisco|0.814806|...
1|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 Clement St|SF|San Francisco|0.814806|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|San Francisco|0.814806|...

Dans la définition Dataset obtenue, toutes les différentes variations de la représentation de « San Francisco » dans les données ont été normalisées dans une même chaîne, « San Francisco ».

Enregistrez cette étape de regroupement probable dans la dernière définition du jeu de données avec `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Étapes suivantes

* Consultez le [tutoriel](tutorial-auto-train-models.md) sur le Machine Learning automatisé pour visualiser un exemple de modèle de régression.

* Consultez la [vue d’ensemble](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) du kit de développement logiciel (SDK) pour obtenir des modèles de conception et des exemples d’utilisation.

* Pour obtenir un exemple d’utilisation de jeux de données, consultez les [exemples de blocs-notes](https://aka.ms/dataset-tutorial).
