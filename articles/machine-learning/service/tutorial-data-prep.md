---
title: 'Tutoriel sur un modèle de régression : Préparer les données'
titleSuffix: Azure Machine Learning service
description: Dans la première partie de ce tutoriel, vous allez apprendre à préparer vos données avec le langage Python pour la modélisation de régression, à l’aide du SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: cforbe
ms.author: cforbe
ms.reviewer: trbye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d20ff1fabfb73c899153cf42bb6f2d7a8f233e21
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314684"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Tutoriel : Préparer les données pour la modélisation de régression

Dans ce tutoriel, vous allez apprendre à préparer des données pour la modélisation de régression, à l’aide du SDK de préparation des données Azure Machine Learning. Effectuez diverses transformations pour filtrer et combiner deux jeux de données NYC Taxi. L’objectif du jeu de données de ce tutoriel est de prédire le coût d’un trajet en taxi en entraînant un modèle avec des caractéristiques de données, comme l’heure de départ, le jour de la semaine, le nombre de passagers et les coordonnées. Ce tutoriel est le premier d’une série de deux.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configurer un environnement Python et importer des packages
> * Charger deux jeux de données avec des noms de champs différents
> * Nettoyer les données pour supprimer les anomalies
> * Effectuer des transformations de données intelligentes pour créer de nouvelles caractéristiques
> * Enregistrer l’objet de dataflow à utiliser dans un modèle de régression

Vous pouvez préparer vos données dans Python avec le [SDK de préparation des données d’Azure Machine Learning](https://aka.ms/data-prep-sdk).

## <a name="get-the-notebook"></a>Obtenir le bloc-notes

Pour des raisons pratiques, ce didacticiel est disponible en tant que [bloc-notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part1-data-prep.ipynb). Exécutez le bloc-notes `regression-part1-data-prep.ipynb` dans des Azure Notebooks ou dans votre propre serveur de bloc-notes Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Importer des packages

Commencez par importer le SDK.


```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Charger des données

Téléchargez les deux jeux de données NYC Taxi dans les objets de dataflow.  Ces jeux de données contiennent des champs légèrement différents. La méthode `auto_read_file()` reconnaît automatiquement le type du fichier d’entrée.


```python
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file will automatically identify and parse the file type, and is useful if you don't know the file type
yellow_df = dprep.auto_read_file(path=yellow_path)

display(green_df.head(5))
display(yellow_df.head(5))
```

## <a name="cleanse-data"></a>Nettoyer les données

Maintenant, vous devez définir certaines variables avec des raccourcis de transformations qui seront appliqués à tous les dataflows. La variable `drop_if_all_null` sera utilisée pour supprimer des enregistrements où tous les champs sont Null. La variable `useful_columns` contient un tableau des descriptions de colonnes qui sont conservées dans chaque dataflow.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Vous transformez d’abord les données des taxis verts afin de pouvoir les combiner aux données des taxis jaunes. Créez un dataflow temporaire `tmp_df`. Appelez les fonctions `replace_na()`, `drop_nulls()` et `keep_columns()` en utilisant les variables de raccourcis de transformations que vous avez créées. Ensuite, renommez toutes les colonnes du dataframe pour que leurs noms soient les mêmes que dans `useful_columns`.


```python
tmp_df = (green_df
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
tmp_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>coût</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74,5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Remplacez la variable `green_df` par les transformations réalisées sur `tmp_df` à l’étape précédente.

```python
green_df = tmp_df
```

Effectuez les mêmes étapes de transformation pour les données des taxis jaunes.


```python
tmp_df = (yellow_df
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
tmp_df.head(5)
```

Là encore, remplacez `yellow_df` par `tmp_df`, puis appelez la fonction `append_rows()` dans les données des taxis verts pour ajouter les données des taxis jaunes, en créant un nouveau dataframe combiné.


```python
yellow_df = tmp_df
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Convertir les types et filtrer 

Regardez les statistiques relatives aux coordonnées de prise en charge et de dépose pour voir comment les données sont distribuées. Tout d’abord, définissez un objet `TypeConverter` pour remplacer le type des champs de latitude et de longitude par le type décimal. Ensuite, appelez la fonction `keep_columns()` pour restreindre la sortie aux champs de latitude et de longitude, puis appelez `get_profile()`.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>max</th>
      <th>Nombre</th>
      <th>Manquant</th>
      <th>Non manquant</th>
      <th>Manquant (%)</th>
      <th>Erreurs</th>
      <th>Vide</th>
      <th>Quantile 0,1 %</th>
      <th>Quantile 1 %</th>
      <th>Quantile 5 %</th>
      <th>Quantile 25 %</th>
      <th>Quantile 50 %</th>
      <th>Quantile 75 %</th>
      <th>Quantile 95 %</th>
      <th>Quantile 99 %</th>
      <th>Quantile 99,9 %</th>
      <th>Écart type</th>
      <th>Moyenne</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115,179337</td>
      <td>0,000000</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88,114046</td>
      <td>-73,961840</td>
      <td>-73,961964</td>
      <td>-73,947693</td>
      <td>-73,922097</td>
      <td>-73,846670</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>18,792672</td>
      <td>-68,833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0,000000</td>
      <td>40,919121</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,000000</td>
      <td>40,682889</td>
      <td>40,675541</td>
      <td>40,721075</td>
      <td>40,756159</td>
      <td>40,803909</td>
      <td>40,849406</td>
      <td>40,870681</td>
      <td>40,891244</td>
      <td>10,345967</td>
      <td>37,936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115,179337</td>
      <td>0,000000</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87,699611</td>
      <td>-73,984734</td>
      <td>-73,985777</td>
      <td>-73,956250</td>
      <td>-73,928948</td>
      <td>-73,866208</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>18,696526</td>
      <td>-68,896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0,000000</td>
      <td>41,008934</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>7 722,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,000000</td>
      <td>40,662763</td>
      <td>40,654851</td>
      <td>40,717821</td>
      <td>40,756534</td>
      <td>40,784688</td>
      <td>40,852437</td>
      <td>40,879289</td>
      <td>40,937291</td>
      <td>10,290780</td>
      <td>37,963774</td>
    </tr>
  </tbody>
</table>



Dans le récapitulatif des statistiques, vous voyez que certaines coordonnées sont manquantes, et que d’autres ne concernent pas la ville de New York. Filtrez les coordonnées de manière à éliminer celles qui ne concernent pas New York en chaînant des commandes de filtre de colonne dans la fonction `filter()`, et en définissant les limites minimales et maximales de chaque champ. Ensuite, rappelez `get_profile()` pour vérifier la transformation.


```python
tmp_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    ) 
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
tmp_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>max</th>
      <th>Nombre</th>
      <th>Manquant</th>
      <th>Non manquant</th>
      <th>Manquant (%)</th>
      <th>Erreurs</th>
      <th>Vide</th>
      <th>Quantile 0,1 %</th>
      <th>Quantile 1 %</th>
      <th>Quantile 5 %</th>
      <th>Quantile 25 %</th>
      <th>Quantile 50 %</th>
      <th>Quantile 75 %</th>
      <th>Quantile 95 %</th>
      <th>Quantile 99 %</th>
      <th>Quantile 99,9 %</th>
      <th>Écart type</th>
      <th>Moyenne</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,078156</td>
      <td>-73,736481</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,076314</td>
      <td>-73,962542</td>
      <td>-73,962893</td>
      <td>-73,948975</td>
      <td>-73,927856</td>
      <td>-73,866662</td>
      <td>-73,830438</td>
      <td>-73,823160</td>
      <td>-73,769750</td>
      <td>0,048711</td>
      <td>-73,913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,575485</td>
      <td>40,879852</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,632884</td>
      <td>40,713105</td>
      <td>40,711600</td>
      <td>40,721403</td>
      <td>40,758142</td>
      <td>40,805145</td>
      <td>40,848855</td>
      <td>40,867567</td>
      <td>40,877690</td>
      <td>0,048348</td>
      <td>40,765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,085747</td>
      <td>-73,720871</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,078828</td>
      <td>-73,985650</td>
      <td>-73,985813</td>
      <td>-73,959041</td>
      <td>-73,936681</td>
      <td>-73,884846</td>
      <td>-73,815507</td>
      <td>-73,776697</td>
      <td>-73,733471</td>
      <td>0,055961</td>
      <td>-73,920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,583530</td>
      <td>40,879734</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>7 059,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,597741</td>
      <td>40,695376</td>
      <td>40,695115</td>
      <td>40,727549</td>
      <td>40,758160</td>
      <td>40,788378</td>
      <td>40,850372</td>
      <td>40,867968</td>
      <td>40,878586</td>
      <td>0,050462</td>
      <td>40,759487</td>
    </tr>
  </tbody>
</table>



Remplacez `combined_df` par les transformations réalisées sur `tmp_df`.


```python
combined_df = tmp_df
```

### <a name="split-and-rename-columns"></a>Scinder les données et renommer les colonnes

Examinez le profil de données de la colonne `store_forward`.


```python
combined_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>max</th>
      <th>Nombre</th>
      <th>Manquant</th>
      <th>Non manquant</th>
      <th>Manquant (%)</th>
      <th>Erreurs</th>
      <th>Vide</th>
      <th>Quantile 0,1 %</th>
      <th>Quantile 1 %</th>
      <th>Quantile 5 %</th>
      <th>Quantile 25 %</th>
      <th>Quantile 50 %</th>
      <th>Quantile 75 %</th>
      <th>Quantile 95 %</th>
      <th>Quantile 99 %</th>
      <th>Quantile 99,9 %</th>
      <th>Écart type</th>
      <th>Moyenne</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>O</td>
      <td>7 059,0</td>
      <td>99,0</td>
      <td>6 960,0</td>
      <td>0,014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Dans le profil des données de `store_forward`, vous voyez que les données sont incohérentes, et que certaines valeurs sont manquantes ou Null. Remplacez ces valeurs à l’aide des fonctions `replace()` et `fill_nulls()`, et dans les deux cas, remplacez-les par la chaîne « N ».


```python
combined_df = combined_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Exécutez une autre fonction `replace`, cette fois sur le champ `distance`. Cela permet de remettre en forme les valeurs de distance mal étiquetées comme `.00`, et remplit toutes les valeurs Null avec des zéros. Convertissez le champ `distance` au format numérique.


```python
combined_df = combined_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
combined_df = combined_df.to_number(["distance"])
```

Scindez les données de prise en charge et de dépose entre la colonne des dates et la colonne des heures. Utilisez `split_column_by_example()` pour effectuer cette répartition. Dans ce cas, le paramètre facultatif `example` de `split_column_by_example()` est omis. Par conséquent, la fonction détermine automatiquement où effectuer le scindement en fonction des données.


```python
tmp_df = (combined_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
tmp_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>coût</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73,937721</td>
      <td>40,758404</td>
      <td>-73,937721</td>
      <td>40,758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73,937691</td>
      <td>40,758419</td>
      <td>-73,937790</td>
      <td>40,758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73,937805</td>
      <td>40,758396</td>
      <td>-73,937775</td>
      <td>40,758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>


Renommez les colonnes générées par `split_column_by_example()` à l’aide de noms explicites.


```python
tmp_df_renamed = (tmp_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
tmp_df_renamed.head(5)
```

Remplacez `combined_df` par les transformations exécutées, puis appelez `get_profile()` pour afficher le récapitulatif des statistiques, une fois toutes les transformations réalisées.


```python
combined_df = tmp_df_renamed
combined_df.get_profile()
```

## <a name="transform-data"></a>Transformer les données

Scindez encore davantage les données de prise en charge et de dépose d’après le jour de la semaine, le jour du mois et le mois. Pour obtenir le jour de semaine, utilisez la fonction `derive_column_by_example()`. Cette fonction prend comme paramètre un tableau d’exemple d’objets qui définissent les données d’entrée et la sortie souhaitée. La fonction détermine alors automatiquement la transformation souhaitée. Pour les colonnes des heures de prise en charge et de dépose, effectuez un scindement des données par heure, minute et seconde, à l’aide de la fonction `split_column_by_example()`, sans exemple de paramètre.

Une fois que vous avez généré ces nouvelles caractéristiques, supprimez les champs d’origine au profit de ces caractéristiques, à l’aide de `drop_columns()`. Renommez tous les champs restants en leur attribuant des noms descriptifs.


```python
tmp_df = (combined_df
    .derive_column_by_example(
        source_columns="pickup_date", 
        new_column_name="pickup_weekday", 
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )
          
    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # the following two split_column_by_example calls reference the generated column names from the above two calls
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time", 
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])
          
    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

tmp_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>coût</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Jeudi</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Jeudi</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Jeudi</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Jeudi</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Mardi</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Mardi</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73,937721</td>
      <td>40,758404</td>
      <td>-73,937721</td>
      <td>40,758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Mardi</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Mardi</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73,937691</td>
      <td>40,758419</td>
      <td>-73,937790</td>
      <td>40,758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Mardi</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Mardi</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73,937805</td>
      <td>40,758396</td>
      <td>-73,937775</td>
      <td>40,758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Dans les données ci-dessus, vous voyez que les composants de date et d’heure pour la prise en charge et la dépose qui ont été obtenus à partir des transformations dérivées sont corrects. Supprimez les colonnes `pickup_datetime` et `dropoff_datetime`, car nous n’en avons plus besoin.


```python
tmp_df = tmp_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Utilisez la fonctionnalité d’inférence de type afin de vérifier automatiquement le type de données de chaque champ, et d’afficher les résultats de l’inférence.


```python
type_infer = tmp_df.builders.set_column_types()
type_infer.learn()
type_infer
```

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

D’après les données, les résultats de l’inférence semblent corrects. Vous pouvez maintenant appliquer les conversions de type au dataflow.


```python
tmp_df = type_infer.to_dataflow()
tmp_df.get_profile()
```

Avant d’empaqueter le flux de données, appliquez deux filtres finaux sur le jeu de données. Pour éliminer les points de données incorrects, filtrez le flux de données sur les enregistrements où les valeurs `cost` et `distance` sont supérieures à zéro.

```python
tmp_df = tmp_df.filter(dprep.col("distance") > 0)
tmp_df = tmp_df.filter(dprep.col("cost") > 0)
```

À ce stade, vous disposez d’un objet de dataflow entièrement transformé et préparé, que vous pouvez utiliser dans un modèle Machine Learning. Le SDK inclut des fonctionnalités de sérialisation d’objet, qui sont utilisées de la façon suivante.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = tmp_df
package = dprep.Package([dflow_prepared])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne souhaitez pas passer à la deuxième partie du tutoriel, supprimez le fichier `dflows.dprep` de votre répertoire actif (que l’exécution soit locale ou dans Azure Notebooks). Si vous passez à la deuxième partie, le fichier `dflows.dprep` devra se trouver dans le répertoire actif.

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de ce tutoriel, vous avez :

> [!div class="checklist"]
> * Configuration de l'environnement de développement
> * Chargé et nettoyé des jeux de données
> * Utilisé des transformations intelligentes pour prédire votre logique, à l’aide d’un exemple
> * Fusionné et empaqueté des jeux de données pour l’entraînement du machine learning

Vous êtes désormais prêt à utiliser ces données d’entraînement dans la suite de cette série de tutoriels :

> [!div class="nextstepaction"]
> [Tutoriel 2 : Entraîner un modèle de régression](tutorial-auto-train-models.md)
