---
title: Explorer et de préparer des données (classe de jeu de données)
titleSuffix: Azure Machine Learning service
description: Explorer les données à l’aide de statistiques de synthèse et préparer des données via le nettoyage des données, de transformation et de l’ingénierie
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: f9087d1fda7574043879983e31d7b608dbe58798
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204965"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Explorer et préparer des données avec la classe de jeu de données (version préliminaire)

Découvrez comment Explorer et préparer des données avec le [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Le [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) classe (version préliminaire) vous permet d’Explorer et de préparer vos données en fournissant des fonctions telles que : échantillonnage, les statistiques de synthèse et les transformations intelligentes. Étapes de transformation sont enregistrés dans [des définitions de Dataset](how-to-manage-dataset-definitions.md) avec la capacité de gérer plusieurs fichiers volumineux de différents schémas de manière hautement évolutive.

> [!Important]
> Certaines classes de jeu de données (version préliminaire) ont des dépendances sur le Kit de développement logiciel de la préparation des données (GA). Bien que les fonctions de transformation peuvent être faites directement avec le GA'ed [fonctions du SDK de la préparation des données](how-to-transform-data.md), nous vous recommandons des wrappers de package de jeu de données décrites dans cet article si vous générez une nouvelle solution. Azure Machine Learning jeux de données (version préliminaire) vous permettent non seulement de convertir vos données, mais également [instantané des données](how-to-create-dataset-snapshots.md) et stocker [définitions de jeu de données avec version](how-to-manage-dataset-definitions.md). Jeux de données est la prochaine version du SDK de la préparation des données, offre des fonctionnalités étendues pour la gestion des jeux de données dans les solutions d’intelligence artificielle.

## <a name="prerequisites"></a>Conditions préalables

Pour Explorer et préparer vos données, vous devez :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

* Un espace de travail de service Microsoft Azure Machine Learning. Consultez [créer un espace de travail du service Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Le Kit de développement Azure Machine Learning pour Python (version 1.0.21 ou une version ultérieure). Pour installer ou mettre à jour vers la dernière version du SDK, consultez [installer ou mettre à jour le SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Azure Machine Learning Data Prep SDK. Pour installer ou mettre à jour vers la dernière version, consultez [installer ou mettre à jour le le Kit de développement de la préparation des données](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Télécharger les exemples de fichiers pour suivre les exemples : [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) et [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>échantillonnage

Prendre un échantillon de vos données pour obtenir une présentation initiale de votre architecture de données et du contenu. À ce stade, le [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) méthode à partir de la classe de jeu de données prend en charge les stratégies d’échantillonnage Top N Simple aléatoire et Stratified.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Exemple de N premiers

Pour un échantillonnage Top N, les n premiers enregistrements de votre jeu de données sont votre exemple. Cela est utile si vous essayez simplement avoir une idée de quel aspect d’enregistrements de données votre comme ou, pour voir quels sont les champs dans vos données.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRATIQUE FRAUDULEUSE|...
1|10516598|HZ258664|4/15/2016 17:00|ENREGISTRER LES MARSHFIELD 082XX S|890|THEFT|...
2|10519196|HZ261252|4/15/2016 10:00|ENREGISTRER LES SACRAMENTO 104XX S|1154|PRATIQUE FRAUDULEUSE|...
3|10519591|HZ261534|4/15/2016 9:00|113XX NREGISTRER PRAIRIE|1120|PRATIQUE FRAUDULEUSE|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="simple-random-sample"></a>Échantillon aléatoire simple

Dans l’échantillonnage aléatoire Simple, chaque membre de la population de données a la même chance d’être sélectionnés dans le cadre de l’exemple. Dans le `simple_random` exemple de stratégie, les enregistrements à partir de votre jeu de données sont sélectionnées selon la probabilité spécifiée et retourne un jeu de données modifiée. Le paramètre de valeur initiale est facultatif.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|ENREGISTRER LES MARSHFIELD 082XX S|890|THEFT|...
1|10519196|HZ261252|4/15/2016 10:00|ENREGISTRER LES SACRAMENTO 104XX S|1154|PRATIQUE FRAUDULEUSE|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="stratified-sample"></a>Échantillon stratifié

Exemples stratifiées vous assurer que certains groupes d’une population sont représentés dans l’exemple. Dans le `stratified` exemple de stratégie, le remplissage est divisé en couches, ou les sous-groupes, selon les similitudes, et les enregistrements sont sélectionnées au hasard à partir de chaque couche en fonction des pondérations couches indiquées par le `fractions` paramètre.

Dans l’exemple suivant, nous regrouper chaque enregistrement par les colonnes spécifiées et inclure l’enregistrement ladite selon les informations de poids de couches X dans `fractions`. Si un couches n’est pas spécifié ou l’enregistrement ne peuvent pas être regroupée, le poids par défaut pour l’exemple est 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|ENREGISTRER LES MARSHFIELD 082XX S|890|THEFT|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...
2|10535059|HZ278872|4/15/2016 4:30|ENREGISTRER LES KILBOURN 004XX S|810|THEFT|...

## <a name="explore-with-summary-statistics"></a>Explorer avec des statistiques de synthèse

 Détecter les anomalies, pas les valeurs, ou le nombre d’erreurs avec le [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) (méthode). Cette fonction obtient le profil et des statistiques de synthèse de vos données, qui à son tour vous aide à déterminent les opérations de préparation des données nécessaires à appliquer.

```Python
dataset.get_profile()
```

||Type|Min|max|Nombre|Manquant|Non manquant|Manquant (%)|Erreurs|Vide|Quantile 0,1 %|Quantile 1 %|Quantile 5 %|Quantile 25 %|Quantile 50 %|Quantile 75 %|Quantile 95 %|Quantile 99 %|Quantile 99,9 %|Moyenne|Écart type|Variance|Asymétrie|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e+07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Case Number|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Block|FieldType.STRING|ENREGISTRER LES KILBOURN 004XX S|113XX NREGISTRER PRAIRIE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primary Type|FieldType.STRING|PRATIQUE FRAUDULEUSE|THEFT|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Description|FieldType.STRING|COCHER ERRONÉ|AU FIL DE 500 $|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Location Description|FieldType.STRING||SCHOOL, PUBLIC, CRÉATION|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Arrest|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domestic|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Le temps|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5.|24|10.0|0.0|10.0|0.0|0.0|0.0|5.|5.|5.|6.|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5.|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Community Area|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI Code|FieldType.INTEGER|6.|11|10.0|0.0|10.0|0.0|0.0|0.0|6.|6.|6.|6.|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X Coordinate|FieldType.INTEGER|1.16309e+06|1.18336e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e + 06|1.16678e + 06|1.17921e + 06|1.18336e + 06|1.18336e + 06|1.18336e + 06|1.17108e + 06|10793.5|1.165e + 08|0.335126|-2.33333
Y Coordinate|FieldType.INTEGER|1.8315e + 06|1.908E + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e + 06|1.8315e + 06|1.8315e + 06|1.83614e + 06|1.85005e + 06|1.89352e + 06|1.908E + 06|1.908E + 06|1.908E + 06|1.86319e + 06|39905.2|1.59243e + 09|0.293465|-2.33333
Year|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Updated On|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Longitude|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Lieu|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Imputer des valeurs manquantes

Dans les jeux de données, les valeurs null, NaN et les valeurs qui ne contiennent aucun contenu sont considérés comme des valeurs manquantes. Ceux-ci peuvent affecter les performances de vos modèles machine learning ou mener à des conclusions non valides. Imputation est une approche courante pour traiter les valeurs manquantes et est utile lorsque vous avez un pourcentage élevé de manquer les enregistrements que vous ne pouvez pas simplement supprimer la valeur.

À partir du profil de jeu de données généré dans la section précédente, nous voyons que `Latitude` et `Longitude` colonnes ont un pourcentage élevé de valeurs manquantes. Dans cet exemple, nous calculer la moyenne et imputer les valeurs manquantes pour ces deux colonnes.

Tout d’abord, obtenez la définition la plus récente du jeu de données avec [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) et alléger les données avec [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#keep-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow), donc nous afficher uniquement les colonnes que nous souhaitons adresse.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ID|Arrest| Latitude|Longitude|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

Ensuite, vérifiez la `MEAN` valeur de la colonne de latitude, en utilisant le [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) (fonction). Cette fonction accepte un tableau de colonnes dans le paramètre `group_by_columns` pour spécifier le niveau d’agrégation. Le `summary_columns` paramètre accepte le `SummaryColumnsValue` (fonction), qui spécifie le nom de colonne actuel, le nom du nouveau champ calculé, et le `SummaryFunction` à effectuer.

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

Une fois que nous avons vérifié les valeurs pour imputer, utilisez [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) pour en savoir plus une expression fixe qui voient les colonnes avec soit un calculé `MIN`, `MAX`, `MEAN` valeur, ou un `CUSTOM` valeur. Quand `group_by_columns` est spécifié, les valeurs manquantes sont imputées par groupe avec `MIN`, `MAX`, et `MEAN` calculées par groupe.

Le [ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) accepte une chaîne column_id et un `ReplaceValueFunction` pour spécifier le type impute. Pour la valeur de longitude manquante, vous devez l’imputer avec-87 en fonction des connaissances externes.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Imputer étapes peuvent être chaînées dans un `ImputeMissingValuesBuilder` à l’aide de l’objet le [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) fonction de la classe [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). Le paramètre `impute_columns` accepte un tableau d’objets `ImputeColumnArguments`.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Appelez le [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) pour stocker les étapes impute de fonction et les appliquer à un objet de flux de données à l’aide [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Comme indiqué dans le tableau de sortie suivant, la latitude manquante a été imputée avec le `MEAN` valeur `Arrest==False` groupe et la longitude manquante a été imputées avec-87.

||ID|Arrest|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

Mettre à jour la définition du Dataset, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) pour conserver les étapes de transformation effectuée.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|Arrest|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Créer des règles d’assertion

Souvent, les données nous travaillons avec lors du nettoyage et préparation des données est uniquement un sous-ensemble des données totales que nous avons besoin pour la production. Par conséquent, certaines hypothèses que nous rendre dans le cadre de notre nettoyage peuvent s’avérer pour être false. Par exemple, dans un jeu de données qui met à jour en continu, une colonne qui contenait initialement numéros dans une certaine plage peut contenir une plage plus large de valeurs dans les exécutions ultérieures. Ces erreurs entraînent souvent dans les pipelines rompus ou des données incorrectes.

Prise en charge des jeux de données créez des assertions sur les données, qui sont évaluées à mesure que le pipeline s’exécute. Ces assertions nous permettent de vérifier que nos hypothèses sur les données continuent à être précis et, si non, pour gérer les défaillances en conséquence.

Par exemple, si vous souhaitez limiter `Latitude` et `Longitude` valeurs dans votre jeu de données à des plages numériques spécifiques, le [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) méthode garantit que c’est toujours le cas.

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

||Type|Min|max|Nombre|Manquant|Non manquant|Manquant (%)|Erreurs|Vide|Quantile 0,1 %|Quantile 1 %|Quantile 5 %|Quantile 25 %|Quantile 50 %|Quantile 75 %|Quantile 95 %|Quantile 99 %|Quantile 99,9 %|Moyenne|Écart type|Variance|Asymétrie|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e+07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Arrest|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
Longitude|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

À partir du profil, vous voyez que le `Error Count` pour la `Longitude` colonne est 3. Le code suivant filtre le jeu de données récupère l’erreur et voit quelle valeur provoque l’assertion échoue. À ce stade, ajustez votre code et nettoyer vos données de manière appropriée.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Dériver des colonnes par exemple

Un des outils plus avancés pour les jeux de données est la possibilité de dériver des colonnes à l’aide des exemples de résultats souhaités. Cela vous permet de donner le SDK un exemple, donc il peut générer du code pour obtenir les transformations prévues.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|Case Number|Date|Block|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|ENREGISTRER LES MARSHFIELD 082XX S|...
2|10519196|HZ261252|2016-04-15 10:00:00|ENREGISTRER LES SACRAMENTO 104XX S|...

Vous avez besoin de transformer le format de date et l’heure à « 2016-04-04 22 h 00 - 12 : 00 ». Dans le [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argument, fournissent des exemples de la sortie souhaitée dans le `example_data` paramètre dans ce format : *(sortie d’origine, résultat souhaité)*.

Le code suivant fournit deux exemples de sortie souhaitée, (« 2016-04-04 23:56:00 », « 2016-04-04 10 PM-12 h ») et ( » 2016-04-15 17:00:00 », « 2016-04-15 16 h 00 - 18 h 00 »)

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Dans le tableau suivant, notez qu’une nouvelle colonne, Date_Time_Range contient des enregistrements dans le format spécifié.

||ID|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10 H - 12 H

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Regroupements floues

Lorsque vous collecter des données provenant de différentes sources, vous pouvez rencontrer des variations d’orthographe, mise en majuscules ou abréviations des entités de mêmes. Automatiquement, normaliser et rapprocher ces variantes avec la fonctionnalité de regroupement probable, ou du clustering de texte, du SDK.

Par exemple, la colonne `inspections.business.city` contient plusieurs formulaires du nom de ville « San Francisco ».

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Aliments Roupies rapide-N-Ezee|3861 24th St|SF|...
1|67565|ROI de café de nouilles thaï|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|ROI de café de nouilles thaï|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|St clement 832|SF|...
4|69186|Restauration premier & événements, Inc.|1255 22 St|S.F.|...

Nous allons utiliser le [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) méthode pour ajouter une colonne avec la forme canonique automatiquement détectée de « San Francisco ». Les arguments `source_column` et `new_column_name` sont requis. Vous avez également la possibilité de :

* Créer une nouvelle colonne, `similarity_score_column_name`, qui affiche le score de similarité entre chaque paire de valeurs d’origine et canoniques.

* Fournir un `similarity_threshold`, qui est le score de similarité minimal pour les valeurs à regrouper.

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
0|16162|Aliments Roupies rapide-N-Ezee|3861 24th St|SF|San Francisco|0.814806|...
1|67565|ROI de café de nouilles thaï|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|ROI de café de nouilles thaï|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|St clement 832|SF|San Francisco|0.814806|...
4|69186|Restauration premier & événements, Inc.|1255 22 St|S.F.|San Francisco|0.814806|...

Dans la définition du Dataset qui en résulte, toutes les différentes variantes de représentation « San Francisco » dans les données ont été normalisées à la même chaîne, « San Francisco ».

Enregistrer cette étape de regroupement approximatif dans la dernière définition de jeu de données avec `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Étapes suivantes

* [Gérer le cycle de vie des définitions de jeu de données](how-to-manage-dataset-definitions.md).

* Consultez l’apprentissage automatisé [didacticiel](tutorial-auto-train-models.md) pour obtenir un exemple de modèle de régression.

* Consultez le kit SDK [vue d’ensemble](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) des modèles de conception et des exemples d’utilisation.

* Pour obtenir un exemple d’utilisation de jeux de données, consultez le [exemples de blocs-notes](https://aka.ms/dataset-tutorial).