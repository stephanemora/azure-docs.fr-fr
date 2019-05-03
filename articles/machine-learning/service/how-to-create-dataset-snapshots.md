---
title: Comparer & reproduire des données au fil du temps avec des captures instantanées
titleSuffix: Azure Machine Learning service
description: Découvrez comment comparer les données au fil du temps et de garantir la reproductibilité des instantanés d’un jeu de données
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: ed10cb259802321769605bc0399a610131ddb174
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029144"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Comparer les données et de garantir la reproductibilité des instantanés (préversion)

Dans cet article, vous apprendrez à créer et gérer des instantanés de votre [jeux de données Azure Machine Learning](how-to-create-register-datasets.md) (jeux de données) afin de capturer ou comparer les données au fil du temps. Jeux de données facilite l’accéder et travailler avec vos données dans le cloud dans différents scénarios. 

**Captures instantanées du jeu de données** stocker un profil (statistiques de synthèse) des données au moment de sa création. Vous pouvez choisir pour également stocker une copie des données de votre instantané de reproductibilité. 

>[!Important]
> Captures instantanées entraînent des frais de stockage. Stocker une copie des données de votre instantané nécessite davantage de stockage. Utilisez [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quand ils ne sont plus nécessaires.

## <a name="when-to-use-snapshots"></a>Quand utiliser des captures instantanées

Il existe trois principales utilisations des instantanés :

+ **Validation de modèle**: Comparer le profil de données de différents instantanés entre les exécutions d’apprentissage ou sur les données de production. 

+ **Modèle reproductibilité**: Reproduire les résultats en appelant un instantané qui inclut des données pendant la formation. 

+ **Suivre les données au fil du temps**: Voir comment le jeu de données a évolué en [comparaison des profils](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Conditions préalables

Pour créer des captures instantanées du jeu de données, vous avez besoin d’un jeu de données inscrits Azure Machine Learning. Si vous n’en avez pas, consultez [créer et inscrire des jeux de données](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Créer des captures instantanées du jeu de données

Pour créer un instantané d’un jeu de données, utilisez [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) à partir du Kit de développement logiciel Azure Machine Learning. 

Par défaut, l’instantané stocke le profil (statistiques de synthèse) des données avec la dernière version [définition du dataset](how-to-manage-dataset-definitions.md) appliqué. Une définition de dataset contient un enregistrement de toutes les étapes transformation définie pour les données. Il est un excellent moyen d’optimiser votre préparation des données reproductible.

Si vous le souhaitez, vous pouvez également inclure une copie des données de votre instantané en ajoutant `create_data_snapshot = True`.  Ces données peuvent être utiles pour la reproductibilité. 

Cet exemple utilise [échantillonner des données de crime](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) et un jeu de données appelé `dataset_crime` créé à l’aide de l’article, [« créer et inscrire des jeux de données »](how-to-create-register-datasets.md).

```Python
from azureml.core.dataset import Workspace, Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.Dataset['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```
 

Étant donné que les instantanés sont créés de façon asynchrone, utilisez la [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) (méthode) pour surveiller le processus.

```python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Sortie :

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Utilisez [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quand ils ne sont plus nécessaires.

## <a name="find-snapshots"></a>Rechercher des captures instantanées

Pour récupérer un instantané existant, utilisez [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Pour obtenir une liste de vos captures instantanées enregistrées d’un jeu de données, utilisez [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--). 

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Obtenir des données et profils à partir d’instantanés

Chaque instantané génère un profil du jeu de données, qui inclut des statistiques de synthèse et vous donne la possibilité d’enregistrer une copie de vos données.

### <a name="get-the-data-profile"></a>Obtenir le profil de données

Utilisez [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) d’accéder au profil de vos données.  Vous pouvez utiliser ce profil pour comparer les données de formation et de production, par exemple.

```Python
snapshot.get_profile()
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


### <a name="get-the-data-from-the-snapshot"></a>Obtenir les données à partir de l’instantané

Pour obtenir une copie des données enregistrées dans un instantané de jeu de données, générer une trame de données pandas avec les [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) (méthode).

Cette méthode échoue si une copie des données n’a pas été demandée lors de la création de capture instantanée. 

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Case Number|Date|Block|IUCR|Primary Type|Description|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|Latitude|Longitude|Lieu
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|PRATIQUE FRAUDULEUSE|USURPATION D’IDENTITÉ FINANCIÈRE SUR 300 DOLLARS|OTHER|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|ENREGISTRER LES MARSHFIELD 082XX S|890|THEFT|À PARTIR DE LA GÉNÉRATION|LIEU DE RÉSIDENCE|False|False|...|21|71|6.|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|ENREGISTRER LES SACRAMENTO 104XX S|1154|PRATIQUE FRAUDULEUSE|USURPATION D’IDENTITÉ FINANCIÈRE 300 DOLLARS ET, SOUS|LIEU DE RÉSIDENCE|False|False|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|


## <a name="next-steps"></a>Étapes suivantes

* Consultez le kit SDK [vue d’ensemble](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) des modèles de conception et des exemples d’utilisation.

* Pour obtenir un exemple d’utilisation de captures instantanées du jeu de données, consultez le [exemples de blocs-notes](https://aka.ms/dataset-tutorial).
