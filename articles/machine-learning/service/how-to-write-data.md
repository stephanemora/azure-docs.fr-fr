---
title: Écrire des données avec le SDK de préparation des données Azure Machine Learning - Python
description: Découvrez comment écrire des données avec le SDK de préparation des données Azure Machine Learning. Vous pouvez écrire des données à tout moment dans un flux de données, ainsi que dans les fichiers situés aux emplacements pris en charge (système de fichiers local, Stockage Blob Azure et Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946763"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>Écrire des données avec le SDK de préparation des données Azure Machine Learning
Vous pouvez écrire des données à tout moment dans un flux de données. Ces opérations d’écriture sont ajoutées en tant qu’étapes au flux de données obtenu, et sont exécutées chaque fois que le flux de données est exécuté. Dans la mesure où il n’existe aucune limitation quant au nombre d’étapes d’écriture qu’un pipeline peu contenir, il est facile d’écrire des résultats intermédiaires à utiliser pour le dépannage ou par les autres pipelines. Il est important de noter que l’exécution de chaque étape d’écriture a pour résultat le tirage (pull) complet des données du flux. Par exemple, un flux de données avec trois étapes d’écriture va lire et traiter chaque enregistrement du jeu de données trois fois.

## <a name="writing-to-files"></a>Écriture de données dans un fichier
Avec le [SDK de préparation des données Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py), vous pouvez écrire des données à tout moment dans les fichiers situés aux emplacements pris en charge (système de fichiers local, Stockage Blob Azure et Azure Data Lake Storage). Les données sont écrites dans plusieurs fichiers de partition pour permettre des écritures parallèles. Une fois qu’une écriture est terminée, un fichier sentinel nommé SUCCESS est également généré. Cela permet de savoir quand une écriture intermédiaire est terminée, sans avoir à attendre la fin du pipeline.

Lorsque vous exécutez un flux de données dans Spark, vous devez écrire les données dans un dossier vide. L’exécution d’une écriture dans un dossier existant échouera. Pour éviter que l’écriture n’échoue, vérifiez que le dossier cible est vide ou utilisez un emplacement cible différent pour chaque exécution.

Le SDK de préparation des données Azure Machine Learning prend en charge les formats de fichier suivants :
-   Fichiers délimités (CSV, TSV, etc.)
-   Fichiers Parquet

Pour cet exemple, commencez par charger des données dans un flux de données. Nous réutiliserons ces données avec différents formats.

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

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

## <a name="delimited-files"></a>Fichiers délimités
La ligne ci-dessous crée un flux de données avec une étape d’écriture. Cependant, l’écriture n’a pas encore eu lieu. Lorsque le flux de données est exécuté, l’écriture est exécutée.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
Maintenant, exécutez le flux de données, qui exécute l’opération d’écriture.
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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

Les données écrites dans les colonnes numériques contiennent plusieurs erreurs, car certains nombres n’ont pas été correctement analysés. Par défaut, lorsqu’elles sont écrites au format CSV, ces valeurs Null sont remplacées par la chaîne « ERROR ». Vous pouvez ajouter des paramètres dans le cadre de votre appel d’écriture et spécifier la chaîne à utiliser pour représenter les valeurs Null.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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
## <a name="parquet-files"></a>Fichiers Parquet

 Comme pour la fonction `write_to_csv` ci-dessus, `write_to_parquet` retourne un nouveau flux de données avec une étape d’écriture Parquet qui sera exécutée lorsque le flux de données le sera.

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 Maintenant, nous exécutons le flux de données, qui exécute l’opération d’écriture.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
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
