---
title: Le contrôle de version et la définition de Dataset
titleSuffix: Azure Machine Learning service
description: Découvrez comment mettre à jour des définitions de jeu de données et de gérer le cycle de vie de définitions
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 7c861c8cdc9985caa42bd2beb5236a4f4e93e4c7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028679"
---
# <a name="update-and-manage-the-lifecycle-of-dataset-definitions"></a>Mettre à jour et gérer le cycle de vie des définitions de jeu de données

Découvrez comment mettre à jour et gérer des définitions de jeu de données avec des jeux de données Azure Machine Learning (version préliminaire).

Les définitions de jeu de données sont les recettes permet de préparer vos jeux de données, qui incluent les deux la référence à votre source de données et les étapes de transformation effectuées. Un jeu de données peut avoir plusieurs définitions, et chaque définition a son propre cycle de vie.

Le scénario suivant est un exemple d’utilisation des définitions de jeu de données :

1. Vous créez un pipeline qui consomme la définition actuelle de votre jeu de données d’apprentissage.
1. Les données sous-jacentes changent ; nouveaux attributs ont été ajoutés.
1. Vous créez une nouvelle version de votre définition qui ajoute des transformations supplémentaires pour gérer les nouveaux attributs.

Dans cet exemple, le pipeline existant continue à consommer de la version de définition d’origine. La nouvelle version de la définition peut être utilisée pour de nouveaux scénarios, tels que l’apprentissage d’un modèle ou la création d’un pipeline.

## <a name="prerequisites"></a>Conditions préalables

Vous devez disposer d’un abonnement Azure et un espace de travail pour enregistrer votre jeu de données afin de gérer le cycle de vie des définitions de jeu de données.

L’exemple de fichier utilisé dans les exemples de ce document est disponible à l’adresse [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="update-dataset-definitions"></a>Mettre à jour des définitions de jeu de données

Tout d’abord nous créer et enregistrer un jeu de données avec votre espace de travail.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

La première définition de jeu de données (`version_id` = 1) est créé lorsque le jeu de données est créé. Chaque fois que vous mettez à jour votre définition de Dataset, un nouveau version_id est affectée à la définition la plus récente.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Arrest</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Cette mise à jour de définition n’efface pas les définitions de jeu de données précédentes. Vous pouvez toujours accéder et utiliser les définitions précédentes.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Case Number</th>
      <th>Date</th>
      <th>Block</th>
      <th>IUCR</th>
      <th>Primary Type</th>
      <th>Description</th>
      <th>Location Description</th>
      <th>Arrest</th>
      <th>Domestic</th>
      <th>...</th>
      <th>Ward</th>
      <th>Community Area</th>
      <th>FBI Code</th>
      <th>X Coordinate</th>
      <th>Y Coordinate</th>
      <th>Year</th>
      <th>Updated On</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Lieu</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>PRATIQUE FRAUDULEUSE</td>
      <td>USURPATION D’IDENTITÉ FINANCIÈRE SUR 300 DOLLARS</td>
      <td>OTHER</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>ENREGISTRER LES MARSHFIELD 082XX S</td>
      <td>890</td>
      <td>THEFT</td>
      <td>À PARTIR DE LA GÉNÉRATION</td>
      <td>LIEU DE RÉSIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6.</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>ENREGISTRER LES SACRAMENTO 104XX S</td>
      <td>1154</td>
      <td>PRATIQUE FRAUDULEUSE</td>
      <td>USURPATION D’IDENTITÉ FINANCIÈRE 300 DOLLARS ET, SOUS</td>
      <td>LIEU DE RÉSIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX NREGISTRER PRAIRIE</td>
      <td>1120</td>
      <td>PRATIQUE FRAUDULEUSE</td>
      <td>CONTREFAÇON</td>
      <td>LIEU DE RÉSIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>THEFT</td>
      <td>À PARTIR DE LA GÉNÉRATION</td>
      <td>SCHOOL, PUBLIC, CRÉATION</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6.</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Comme nous pouvons le constater à partir du résultat, la première version de la définition du jeu de données conserve toujours toutes les colonnes à partir des données crime, quel que soit la mise à jour ultérieure. Par exemple, si vous avez un apprentissage modèle actuellement de consommation de la première version du jeu de données et de l’utilisation `Date` colonne du jeu de données en tant que l’une des fonctionnalités, vous ne rencontrerez pas de toute erreur avec la dernière mise à jour définition, qui conserve uniquement `ID`, `Arrest`, `Latitude`, `Longitude` colonnes à partir des données crime.

## <a name="how-to-access-dataset-definitions"></a>Comment accéder aux définitions de jeu de données

Pour obtenir une liste de toutes les définitions, utilisez `get_definitions()`. Pour obtenir une version spécifique d’une définition, utilisez `get_definition()`. L’exemple suivant illustre la récupération d’une liste de toutes les définitions et récupérer la version 1 :

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

La sortie de `get_definitions()` est similaire à l’exemple suivant :

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

Une fois que vous avez récupéré une définition, vous pouvez l’utiliser avec vos modèles machine learning ou dans un pipeline d’apprentissage automatique.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Gérer le cycle de vie des définitions de jeu de données

Vous pouvez gérer le cycle de vie de chaque définition de Dataset indépendamment. Il existe trois phases du cycle de vie : actif, déconseillées ou archivé.

### <a name="active"></a>Actif

Lorsqu’une nouvelle définition de jeu de données est créée, il est actif par défaut. 

### <a name="deprecate"></a>Déconseiller

Définitions de jeu de données peuvent être déconseillées lorsque l’utilisation n’est plus recommandée, et un remplacement est disponible. Lorsqu’une définition de jeu de données obsolète est utilisée dans des pipelines d’apprentissage machine, un message d’avertissement est retourné, mais l’exécution n’est pas bloquée.

Lors de la dépréciation d’une définition de jeu de données, spécifiez l’ID de jeu de données et l’ID de version de définition de jeu de données pour la définition de remplacement. Ces informations sont stockées et utilisées dans le message d’avertissement lorsque vous essayez d’utiliser une définition de jeu de données obsolète.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Archivage

Définitions de DataSet peuvent être archivées lorsque les définitions ne sont pas censées être utilisées pour une raison quelconque (par exemple, les données sous-jacentes n’est plus disponibles). Lorsqu’une définition de jeu de données archivée est utilisée dans des pipelines d’apprentissage machine, l’exécution est bloquée avec erreur.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Réactiver

Vous pouvez réactiver une définition de jeu de données déconseillée ou archivée facilement.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des jeux de données, consultez [créer et inscrire les jeux de données Azure Machine Learning](how-to-create-register-datasets.md).

Pour obtenir un exemple d’utilisation de jeux de données, consultez le [exemples de blocs-notes](https://aka.ms/dataset-tutorial).