---
title: 'Tutoriel : Démarrer l’analyse des données dans les comptes de stockage'
description: Dans ce tutoriel, vous allez découvrir comment analyser des données qui se trouvent dans un compte de stockage.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 2a22174fb23a4f0f7bebd58e276a6778e986ce9e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322925"
---
# <a name="analyze-data-in-a-storage-account"></a>Analyser des données dans un compte de stockage

Dans ce tutoriel, vous allez découvrir comment analyser des données qui se trouvent dans un compte de stockage.

## <a name="overview"></a>Vue d’ensemble

Jusqu’à présent, nous avons vu des scénarios où les données se trouvent dans des bases de données de l’espace de travail. Maintenant, nous allons voir comment utiliser des fichiers dans des comptes de stockage. Dans ce scénario, nous utilisons le compte de stockage principal de l’espace de travail et le conteneur que nous avons spécifiés au moment de la création de l’espace de travail.

* Le nom du compte de stockage est **contosolake**
* Le nom du conteneur dans le compte de stockage est **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Créer des fichiers CSV et Parquet dans votre compte de stockage

Exécutez le code suivant dans un notebook. Il crée un fichier CSV et un fichier Parquet dans le compte de stockage.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analyser des données dans un compte de stockage

1. Dans Synapse Studio, accédez au hub **Données**, puis sélectionnez **Liées**.
1. Accédez à **Comptes de stockage** > **myworkspace (Principal - contosolake)** .
1. Sélectionnez **utilisateurs (Principaux)** . Vous devez normalement voir le dossier **NYCTaxi**, qui contient les deux dossiers **PassengerCountStats.csv** et **PassengerCountStats.parquet**.
1. Ouvrez le dossier **PassengerCountStats.parquet**. Vous y verrez un fichier parquet avec un nom comme `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
1. Cliquez avec le bouton droit sur **.parquet**, puis sélectionnez **nouveau notebook**. Cette action crée un notebook contenant une cellule semblable à celle-ci :

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Exécutez la cellule.
1. Cliquez avec le bouton droit sur le fichier Parquet qui s’y trouve, puis sélectionnez **Nouveau script SQL** > **Sélectionner les 100 premières lignes**. Cela crée un script SQL semblable à ceci :

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    Dans la fenêtre du script, le champ **Se connecter à** est défini sur **Pool SQL serverless**.

1. Exécutez le script.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Orchestrer des activités avec des pipelines](get-started-pipelines.md)
