---
title: 'Écriture : Kit de développement logiciel (SDK) Python de préparation des données'
titleSuffix: Azure Machine Learning service
description: Découvrez comment écrire des données avec le SDK de préparation des données Azure Machine Learning. Vous pouvez écrire des données à tout moment dans un flux de données, ainsi que dans les fichiers situés aux emplacements pris en charge (système de fichiers local, Stockage Blob Azure et Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1e508d4c7ed8a8d7df8e9ae586c74258958838e9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239823"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Écrire des données avec le SDK de préparation des données d’Azure Machine Learning

Cet article présente différentes méthodes permettant d’écrire des données à l’aide du kit SDK de préparation des données Azure Machine Learning. Les données de sortie peuvent être écrites en tout point du flux de données. Les écritures, ajoutées aux étapes du flux de données obtenu, sont effectuées à chaque exécution du flux de données. Les données sont écrites dans plusieurs fichiers de partition pour permettre des écritures parallèles.

Dans la mesure où il n’existe pas de limitation quant au nombre d’étapes d’écriture qu’un pipeline peu contenir, vous pouvez facilement d’écrire des étapes pour obtenir des résultats intermédiaires utilisables pour la résolution des problèmes ou dans d’autres pipelines.

Chaque fois que vous exécutez une étape d’écriture, une extraction complète des données du flux de données est effectuée. Par exemple, un flux de données comportant trois étapes d’écriture lit et traite trois fois chacun des enregistrements du jeu de données.

## <a name="supported-data-types-and-location"></a>Types de données pris en charge et emplacement

Les formats de fichier suivants sont pris en charge.
-   Fichiers délimités (CSV, TSV, etc.)
-   Fichiers Parquet

Avec le [SDK Python de préparation des données d’Azure Machine Learning](https://aka.ms/data-prep-sdk), vous pouvez écrire des données sur :
+ un système de fichiers local
+ un stockage Azure Blob
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Considérations sur Spark

Lorsque vous exécutez un flux de données dans Spark, vous devez écrire les données dans un dossier vide. Une tentative pour effectuer une écriture dans un dossier existant provoque un échec. Pour éviter que l’écriture n’échoue, vérifiez que le dossier cible est vide ou utilisez un emplacement cible différent pour chaque exécution.

## <a name="monitoring-write-operations"></a>Supervision des opérations d’écriture

Pour des raisons pratiques, un fichier sentinelle nommé SUCCESS est généré une fois qu’une écriture est terminée. Sa présence vous permet de savoir quand une écriture intermédiaire est effectuée, sans devoir attendre la fin du pipeline.

## <a name="example-write-code"></a>Exemple de code d’écriture

Pour cet exemple, commencez par charger des données dans un flux de données. Vous réutiliserez ces données sous différents formats.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```

Exemple de sortie :
|   |  Colonne1 |    Colonne2 | Colonne3 | Colonne4  |Colonne5   | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   Aucun|       NON|     NON  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   Aucun|       NON|     NON  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    Aucun|   NON| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    Aucun|   NON| NON| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    Aucun|   NON| NON| ENSO|   59783.0|    5350.0| 500.0|
|5.| 10015.0|    99999.0|    Aucun|   NON| NON| ENBL|   61383.0|    5867.0| 3270.0|
|6.| 10016.0 |99999.0|   Aucun|   NON| NON|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    Aucun|   NON| NON| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    Aucun|   NON| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    Aucun|   NON| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>Exemple de fichier délimité

Le code suivant utilise la fonction `write_to_csv` pour écrire des données dans un fichier délimité.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Exemple de sortie :
|   |  Colonne1 |    Colonne2 | Colonne3 | Colonne4  |Colonne5   | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   ERROR |       NON|     NON  |   ENRS    |ERROR    |   ERROR |   ERROR|    
|   1|      10003.0 |   99999.0 |   ERROR |       NON|     NON  |   ENSO|       ERROR|        ERROR |ERROR|   
|   2|  10010.0|    99999.0|    ERROR |   NON| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    ERROR |   NON| NON| |   ERROR|    ERROR|    ERROR|
|4| 10014.0|    99999.0|    ERROR |   NON| NON| ENSO|   59783.0|    5350.0| 500.0|
|5.| 10015.0|    99999.0|    ERROR |   NON| NON| ENBL|   61383.0|    5867.0| 3270.0|
|6.| 10016.0 |99999.0|   ERROR |   NON| NON|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    ERROR |   NON| NON| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    ERROR |   NON| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    ERROR |   NON| SV|     |77000.0|   15500.0|    120.0|

Dans la sortie précédente, plusieurs erreurs apparaissent dans les colonnes numériques, car certains nombres n’ont pas été analysés correctement. Lorsqu’elles sont écrites au format CSV, les valeurs Null sont par défaut remplacées par la chaîne « ERROR ».

Ajoutez des paramètres à votre appel d’écriture et spécifiez la chaîne à utiliser pour représenter les valeurs Null.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Le code précédent produit cette sortie :
|   |  Colonne1 |    Colonne2 | Colonne3 | Colonne4  |Colonne5   | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NON|     NON  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NON|     NON  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NON| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NON| NON| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NON| NON| ENSO|   59783.0|    5350.0| 500.0|
|5.| 10015.0|    99999.0|    BadData |   NON| NON| ENBL|   61383.0|    5867.0| 3270.0|
|6.| 10016.0 |99999.0|   BadData |   NON| NON|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NON| NON| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NON| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NON| SV|     |77000.0|   15500.0|    120.0|

### <a name="parquet-file-example"></a>Exemple de fichier Parquet

Da façon similaire à `write_to_csv`, la fonction `write_to_parquet` retourne un nouveau flux de données avec une étape d’écriture Parquet qui est exécutée lors de l’exécution du flux de données.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Exécutez le flux de données pour lancer l’opération d’écriture.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

Le code précédent produit cette sortie :
|   |  Colonne1 |    Colonne2 | Colonne3 | Colonne4  |Colonne5   | Colonne6 | Colonne7 | Colonne8 | Colonne9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NON|     NON  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NON|     NON  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NON| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NON| NON| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NON| NON| ENSO|   59783.0|    5350.0| 500.0|
|5.| 10015.0|    99999.0|    MiscreantData |   NON| NON| ENBL|   61383.0|    5867.0| 3270.0|
|6.| 10016.0 |99999.0|   MiscreantData |   NON| NON|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NON| NON| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NON| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NON| SV|     |77000.0|   15500.0|    120.0|
