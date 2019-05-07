---
title: 'Tutoriel sur un modèle de régression : Préparer les données'
titleSuffix: Azure Machine Learning service
description: Dans la première partie de ce tutoriel, vous apprenez à préparer des données en langage Python à la modélisation de régression, à l’aide du SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sihhu
ms.author: MayMSFT
ms.reviewer: trbye
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: a717fa4191c57c21705d24884397ebb485726492
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025117"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Didacticiel : Préparer les données pour la modélisation de régression

Dans ce tutoriel, vous apprenez à préparer des données pour la modélisation de régression à l’aide du [SDK de préparation des données Azure Machine Learning pour Python](https://aka.ms/data-prep-sdk). Vous exécutez diverses transformations pour filtrer et combiner deux jeux de données NYC Taxi différents.

Ce tutoriel est le **premier d’une série de deux**. Une fois que vous aurez terminé la série de tutoriels, vous pourrez prévoir le coût d’une course en taxi en entraînant un modèle sur les caractéristiques des données. Ces caractéristiques incluent le jour et l’heure de départ de la course, le nombre de passagers et le lieu du ramassage.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configurer un environnement Python et importer des packages.
> * Charger deux jeux de données avec des noms de champs différents.
> * Nettoyer des données pour supprimer les anomalies.
> * Effectuer des transformations de données intelligentes pour créer de nouvelles caractéristiques.
> * Enregistrer l’objet de dataflow à utiliser dans un modèle de régression.

## <a name="prerequisites"></a>Prérequis

Passez à la section [Configurer votre environnement de développement](#start) pour lire les étapes relatives aux notebooks, ou utilisez les instructions ci-dessous pour obtenir le notebook et l’exécuter sur Azure Notebooks ou votre propre serveur de notebooks. Pour exécuter le notebook, vous devez disposer des éléments suivants :

* Un serveur de notebooks Python 3.6 sur lequel les éléments suivants sont installés :
    * Le SDK de préparation des données Azure Machine Learning pour Python
* Le notebook du tutoriel

* Utiliser un [serveur de notebooks cloud dans votre espace de travail](#azure) 
* Utiliser [votre propre serveur de notebooks](#server)

### <a name="azure"></a>Utiliser un serveur de notebooks cloud dans votre espace de travail

Il est facile de bien démarrer avec votre propre serveur de notebooks basé sur le cloud. Le [kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) est déjà installé et configuré pour vous une fois que vous avez créé cette ressource cloud.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Après avoir lancé la page web de notebook, exécutez le notebook **tutorials/regression-part1-data-prep.ipynb**.

### <a name="server"></a>Utiliser votre propre serveur de notebooks Jupyter

Utilisez ces étapes pour créer une instance locale de serveur de notebooks Jupyter sur votre ordinateur.  Après avoir terminé les étapes, exécutez le notebook **tutorials/regression-part1-data-prep.ipynb**.

1. Effectuez les étapes d’installation décrites dans le [[Guide de démarrage rapide Python d’Azure Machine Learning](quickstart-run-local-notebook.md)](setup-create-workspace.md#python) pour créer un environnement Miniconda.  Vous pouvez ignorer la section **Créer un espace de travail** si vous le souhaitez, mais vous en aurez besoin pour faire la [partie 2](tutorial-auto-train-models.md) de cette série de tutoriels.
1. Installez le SDK de préparation des données dans votre environnement à l’aide de `pip install azureml-dataprep`.
1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>Configurer votre environnement de développement

Toute la configuration pour votre travail de développement peut être effectuée dans un bloc-notes Python. La configuration comprend les actions suivantes :

* Installer le Kit de développement logiciel (SDK)
* Importer des packages Python

### <a name="install-and-import-packages"></a>Installer et importer des packages

Exécutez la commande suivante pour installer les packages nécessaires si vous ne les avez pas déjà.

```shell
pip install "azureml-dataprep[pandas]>=1.1.0,<1.2.0"
```

Importez le SDK.

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> Veillez à installer la version la plus récente. Ce tutoriel ne fonctionnera pas avec un numéro de version inférieur à 1.1.0

## <a name="load-data"></a>Charger des données

Téléchargez les deux jeux de données NYC Taxi dans les objets de dataflow. Les jeux de données ont des champs légèrement différents. La méthode `auto_read_file()` reconnaît automatiquement le type du fichier d’entrée.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

Un objet `Dataflow` est semblable à un dataframe et représente une série d’opérations immuables et évaluées tardivement sur des données. Les opérations peuvent être ajoutées en appelant les différentes méthodes de transformation et de filtrage disponibles. Le résultat de l’ajout d’une opération à un `Dataflow` est toujours un nouvel objet `Dataflow`.

## <a name="cleanse-data"></a>Nettoyer les données

Maintenant, vous devez définir certaines variables avec des raccourcis de transformations à appliquer à tous les dataflows. La variable `drop_if_all_null` est utilisée pour supprimer les enregistrements où tous les champs sont Null. La variable `useful_columns` contient un tableau des descriptions de colonnes qui sont conservées dans chaque dataflow.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Vous transformez d’abord les données des taxis verts afin de pouvoir les combiner aux données des taxis jaunes. Appelez les fonctions `replace_na()`, `drop_nulls()` et `keep_columns()` en utilisant les variables de raccourcis de transformations que vous avez créées. Ensuite, renommez toutes les colonnes du dataframe pour que leurs noms soient les mêmes que dans la variable `useful_columns`.


```python
green_df = (green_df_raw
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
green_df.head(5)
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

Effectuez les mêmes étapes de transformation pour les données des taxis jaunes. Ces fonctions garantissent que les données nulles sont supprimées du jeu de données, ce qui permet d’améliorer la précision du modèle Machine Learning.

```python
yellow_df = (yellow_df_raw
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
yellow_df.head(5)
```

Appelez la fonction `append_rows()` sur les données des taxis verts pour ajouter les données des taxis jaunes. Un nouveau dataframe combiné est créé.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Convertir les types et filtrer

Regardez les statistiques relatives aux coordonnées de prise en charge et de dépose pour voir comment les données sont distribuées. Tout d’abord, définissez un objet `TypeConverter` pour modifier les champs de latitude et longitude en type décimal. Ensuite, appelez la fonction `keep_columns()` pour restreindre la sortie aux seuls champs de latitude et longitude, puis appelez la fonction `get_profile()`. Ces appels de fonction créent une vue condensée du dataflow pour afficher uniquement les champs lat/long, ce qui facilite l’évaluation des coordonnées manquantes ou hors de portée.


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
      <th>Type</th>
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



Dans le récapitulatif des statistiques, vous voyez que certaines coordonnées sont manquantes, et que d’autres ne concernent pas la ville de New York (c’est une analyse subjective qui le détermine). Filtrez les coordonnées pour exclure les localisations situées en dehors des limites de la ville. Chaînez les commandes de filtre des colonnes au sein de la fonction `filter()` et définissez les limites minimales et maximales de chaque champ. Rappelez ensuite la fonction `get_profile()` pour vérifier la transformation.


```python
latlong_filtered_df = (combined_df
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
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
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

### <a name="split-and-rename-columns"></a>Scinder les données et renommer les colonnes

Examinez le profil de données de la colonne `store_forward`. Ce champ est un indicateur booléen qui a la valeur `Y` quand le taxi n’avait pas de connexion au serveur après la course. Il a donc dû stocker les données relatives à la course en mémoire et les transférer ultérieurement au serveur une fois connecté.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
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



Notez que la sortie du profil de données dans la colonne `store_forward` montre que les données ne sont pas cohérentes et que des valeurs sont manquantes ou Null. Utilisez les fonctions `replace()` et `fill_nulls()` pour remplacer ces valeurs par la chaîne « N » :


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Exécutez la fonction `replace` sur le champ `distance`. La fonction reformate les valeurs de distance mal étiquetées comme `.00`, puis remplit toutes les valeurs Null avec des zéros. Convertissez le champ `distance` au format numérique. Ces points de données incorrects sont des anomalies probables dans le système de collecte de données sur les taxis.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Scindez les valeurs de date/heure de départ et d’arrivée dans les colonnes de date et heure respectives. Utilisez la fonction `split_column_by_example()` pour effectuer le scindement. Dans ce cas, le paramètre facultatif `example` de la fonction `split_column_by_example()` est omis. Par conséquent, la fonction détermine automatiquement où effectuer le scindement en fonction des données.


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
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

Renommez les colonnes générées par la fonction `split_column_by_example()` pour utiliser des noms significatifs.

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

Appelez la fonction `get_profile()` pour voir le récapitulatif complet des statistiques après toutes les étapes de nettoyage.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Transformer les données

Scindez les dates de départ et d’arrivée en valeurs de jour de la semaine, de jour du mois et de mois. Pour obtenir la valeur du jour de la semaine, utilisez la fonction `derive_column_by_example()`. Celle-ci prend un paramètre de tableau d’exemples d’objets qui définissent les données d’entrée et la sortie préférée. La fonction détermine automatiquement la transformation préférée. Pour les colonnes d’heure de départ et d’arrivée, scindez l’heure en heure, minute et seconde à l’aide de la fonction `split_column_by_example()` sans exemple de paramètre.

Après avoir généré les nouvelles caractéristiques, utilisez la fonction `drop_columns()` pour supprimer les champs d’origine, car les nouvelles caractéristiques générées sont préférées. Renommez le reste des champs pour utiliser des descriptions significatives.

Le fait de transformer les données de cette façon pour créer des fonctionnalités à durée définie améliore la précision du modèle Machine Learning. Par exemple, la génération d’une nouvelle fonctionnalité pour le jour de la semaine permet d’établir une relation entre le jour de la semaine et le prix des courses de taxi, qui est souvent plus élevé certains jours de la semaine en raison d’une forte demande.


```python
transformed_features_df = (renamed_col_df
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
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
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

transformed_features_df.head(5)
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

Remarquez que les données montrent que les composants de date et d’heure de départ et d’arrivée qui ont été obtenus à partir des transformations dérivées sont corrects. Supprimez les colonnes `pickup_datetime` et `dropoff_datetime` car elles ne sont pas plus nécessaires (les fonctionnalités temporelles précises, comme l’heure, la minute et la seconde sont plus utiles pour l’entraînement du modèle).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Utilisez la fonctionnalité d’inférence de type afin de vérifier automatiquement le type de données de chaque champ, et d’afficher les résultats de l’inférence.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

La sortie de `type_infer` obtenue se présente comme suit.

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

Les résultats d’inférence paraissent corrects en fonction des données. Appliquez maintenant les conversions des types au dataflow.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Avant d’empaqueter le dataflow, exécutez deux derniers filtres sur le jeu de données. Pour éliminer les points de données capturés de façon incorrecte, filtrez le dataflow sur les enregistrements où les valeurs des variables `cost` et `distance` sont toutes les deux supérieures à zéro. Cette étape améliore considérablement la précision du modèle Machine Learning, car les points de données dont le coût ou la distance est nul représentent les principales valeurs hors norme qui altèrent la précision de la prédiction.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Vous disposez maintenant d’un objet de dataflow entièrement transformé et préparé, que vous pouvez utiliser dans un modèle Machine Learning. Le SDK inclut des fonctionnalités de sérialisation d’objets, qui sont utilisées comme indiqué dans le code suivant.

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
final_df.save(file_path)
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour poursuivre avec la seconde partie du tutoriel, vous avez besoin du fichier **dflows.dprep** situé dans le répertoire actif.

Si vous n’envisagez pas de passer à la seconde partie, supprimez le fichier **dflows.dprep** de votre répertoire actif. Supprimez ce fichier, que vous utilisiez l’exécution localement ou dans [Azure Notebooks](https://notebooks.azure.com/).

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de ce tutoriel, vous avez :

> [!div class="checklist"]
> * Configurer l’environnement de développement
> * Chargé et nettoyé des jeux de données.
> * Utilisé des transformations intelligentes pour prédire votre logique, à l’aide d’un exemple.
> * Fusionné et empaqueté des jeux de données pour l’entraînement du machine learning.

Vous êtes prêt à utiliser les données d’entraînement dans la seconde partie du tutoriel :

> [!div class="nextstepaction"]
> [Tutoriel (seconde partie) : Entraîner le modèle de régression](tutorial-auto-train-models.md)
