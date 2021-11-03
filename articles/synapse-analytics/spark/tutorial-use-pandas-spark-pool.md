---
title: 'Tutoriel : Utilisez Pandas pour lire/écrire des données ADLS dans un pool Apache Spark sans serveur dans Synapse Analytics'
description: Tutoriel sur l’utilisation de Pandas dans un bloc-notes PySpark pour lire/écrire des données ADLS dans un pool Apache Spark sans serveur.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4f977ad6a6cc38a269eedbe3e107b166bd74a4f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028912"
---
# <a name="tutorial-use-pandas-to-readwrite-azure-data-lake-storage-gen2-data-in-serverless-apache-spark-pool-in-synapse-analytics"></a>Tutoriel : Utilisez Pandas pour lire/écrire des données ADLS dans un pool Apache Spark sans serveur dans Synapse Analytics

Découvrez comment utiliser Pandas pour lire/écrire des données sur Azure Data Lake Storage Gen2 (ADLS) à l’aide d’un pool d’Apache Spark sans serveur dans Azure Synapse Analytics. Les exemples de ce tutoriel vous montrent comment lire des données CSV avec Pandas dans Synapse, ainsi que des fichiers Excel et parquet.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

   > [!div class="checklist"]
   > - Lire/écrire des données ADLS Gen2 à l’aide de Pandas dans une session Spark.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut (ou stockage primaire). Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Un pool Apache Spark sans serveur dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations, consultez [Créer un pool Spark dans Azure Synapse](../get-started-analyze-spark.md).
- Configurez le compte de [Azure Data Lake Storage Gen2 secondaire](../../storage/blobs/create-data-lake-storage-account.md) (qui n’est pas la valeur par défaut de l’espace de travail Synapse). Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Créer des services liés - Dans Azure Synapse Analytics, un service lié définit vos informations de connexion au service. Dans ce tutoriel, vous allez ajouter un service lié à Azure Synapse Analytics et Azure Data Lake Storage Gen2.

   1. Ouvrez Azure Synapse Studio et sélectionnez l’onglet **Gérer**.
   1. Sous **Connexions externes**, sélectionnez **Services liés**.
   1. Pour ajouter un service lié, sélectionnez **Nouveau**.
   1. Sélectionnez la vignette Azure Data Lake Storage Gen2 dans la liste, puis sélectionnez **Continuer**.
   1. Entrez vos informations d’identification d’authentification. Les types d'authentification actuellement pris en charge sont la clé de compte, le principal de service (SP), les justificatifs d'identité et l'identité de service modifiée (MSI). Assurez-vous que le *contributeur Storage Blob Data* est affecté au stockage pour SP et MSI avant de le choisir pour l'authentification. **Tester la connexion** pour vérifier que vos informations sont correctes. Sélectionnez **Créer**.

   :::image type="content" source="media/tutorial-use-pandas-spark-pool/create-adls-linked-service.png" alt-text="Capture d’écran de la création d’un service lié à l’aide d’une clé d’accès de stockage ADLS Gen2.":::


> [!NOTE]
> - La fonctionnalité Pandas est prise en charge par **Python 3.8** et le pool Apache Spark sans serveur **Spark3** dans Azure Synapse Analytics. 
> - Prise en charge disponible pour les versions suivantes : **Pandas 2021.10.0, fsspec 0.7.7, adlfs 0.7.7**
> - disposer de capacités pour prendre en charge à la fois **Azure Data Lake Storage Gen2 URI** (abfs [s] ://file_system_name@account_name.dfs.core.windows.net/file_path ) et **FSSPEC short URL** (abfs [s]://container_name/file_path).


## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).


## <a name="readwrite-data-to-default-adls-storage-account-of-synapse-workspace"></a>Lire/écrire des données dans le compte de stockage ADLS par défaut de l’espace de travail Synapse

Pandas peut lire/écrire des données ADLS en spécifiant directement le chemin d’accès du fichier.

Exécutez le code suivant.

   > [!NOTE]
   > Mettez à jour l’URL du fichier dans ce script avant de l’exécuter.

   ```PYSPARK
      #Read data file from URI of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path')
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
   
      #read csv file
      df = pandas.read_csv('abfs[s]://container_name/file_path')
      print(df)
   
      #write csv file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path')
   ```

## <a name="readwrite-data-using-secondary-adls-account"></a>Lecture/écriture de données à l’aide d’un compte ADLS secondaire

Pandas peut lire/écrire les données du compte ADLS secondaire :
   - utilisation du service lié (avec les options d’authentification-clé du compte de stockage, principal du service, gère l’identité du service et les informations d’identification).
   - utilisation des options de stockage pour transmettre directement l'ID et le secret du client, la clé SAS, la clé du compte de stockage et la chaîne de connexion.

**Utilisation du service lié**

Exécutez le code suivant.

   > [!NOTE]
   > Mettez à jour l’URL du fichier et le nom du service lié dans ce script avant de l’exécuter.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'linked_service' : 'linked_service_name'})
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options =       {'linked_service' : 'linked_service_name'})
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'linked_service' :    'linked_service_name'})
   ```


**Utiliser les options de stockage** pour transmettre directement l'ID et le secret du client, la clé SAS, la clé du compte de stockage et la chaîne de connexion.

Exécutez le code suivant.

   > [!NOTE]
   > Mettez à jour l’URL du fichier et storage_options dans ce script avant de l’exécuter.

   ```PYSPARK
      #Read data file from URI of secondary Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   file_path', storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://file_system_name@account_name.dfs.core.windows.net/file_path',    storage_options = {'account_key' : 'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```

   ```PYSPARK
      #Read data file from FSSPEC short URL of default Azure Data Lake Storage Gen2
   
      import pandas
      
      #read data file
      df = pandas.read_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   
      print(df)
      
      #write data file
      data = pandas.DataFrame({'Name':['A', 'B', 'C', 'D'], 'ID':[20, 21, 19, 18]})
      data.to_csv('abfs[s]://container_name/file_path', storage_options = {'account_key' :    'account_key_value'})
    
      ## or storage_options = {'sas_token' : 'sas_token_value'}
      ## or storage_options = {'connection_string' : 'connection_string_value'}
      ## or storage_options = {'tenant_id': 'tenant_id_value', 'client_id' : 'client_id_value',    'client_secret': 'client_secret_value'}
   ```


## <a name="example-to-readwrite-parquet-file"></a>Exemple de lecture/écriture de fichier parquet 

Exécutez le code suivant.

   > [!NOTE]
   > Mettez à jour l’URL du fichier dans ce script avant de l’exécuter.

   ```PYSPARK
      import pandas
      
      #read parquet file
      df = pandas.read_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
      print(df)
      
      #write parquet file
      df.to_parquet('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   parquet_file_path')
   ```

## <a name="example-to-readwrite-excel-file"></a>Exemple de lecture/écriture dans un fichier Excel 

Exécutez le code suivant.

   > [!NOTE]
   > Mettez à jour l’URL du fichier dans ce script avant de l’exécuter.

   ```PYSPARK
      import pandas
      
      #read excel file
      df = pandas.read_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/   excel_file_path')
      print(df)
      
      #write excel file
      df.to_excel('abfs[s]://file_system_name@account_name.dfs.core.windows.net/excel_file_path')
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Azure Synapse Analytics](../index.yml)
