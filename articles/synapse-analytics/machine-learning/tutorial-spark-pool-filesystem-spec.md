---
title: 'Tutoriel : Utiliser FSSPEC pour lire/écrire des données ADLS dans le pool Spark Synapse Analytics'
description: Tutoriel montrant comment utiliser FSSPEC pour permettre aux charges de travail de machine learning de lire/d’écrire des données ADLS dans des pools Spark dédiés.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 06/07/2021
author: AjAgr
ms.author: ajagarw
ms.openlocfilehash: ecd0b923939ab1da724b46272dbaf60c44389d3d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904306"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-synapse-analytics-spark-pool"></a>Tutoriel : Utiliser FSSPEC pour lire/écrire des données ADLS dans le pool Spark Synapse Analytics

Découvrez comment utiliser FSSPEC (Filesystem Spec) pour lire/écrire des données sur ADLS (Azure Data Lake Storage) à l’aide d’un service lié dans un pool Spark dédié au sein d’Azure Synapse Analytics.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> - Lire/écrire des données ADLS dans une session Spark dédiée.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Pool Spark dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations, consultez [Créer un pool Spark dans Azure Synapse](../get-started-analyze-spark.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-linked-services"></a>Créez des services liés

Dans Azure Synapse Analytics, un service lié vous permet de définir vos informations de connexion à d’autres services. Dans cette section, vous allez ajouter un service lié Azure Synapse Analytics et Azure Data Lake Storage Gen2.

1. Ouvrez Azure Synapse Studio et sélectionnez l’onglet **Gérer**.
1. Sous **Connexions externes**, sélectionnez **Services liés**.
1. Pour ajouter un service lié, sélectionnez **Nouveau**.
1. Sélectionnez la vignette Azure Data Lake Storage Gen2 dans la liste, puis sélectionnez **Continuer**.
1. Entrez vos informations d’identification d’authentification. La clé de compte est le type d’authentification pris en charge. Sélectionnez **Tester la connexion** pour vérifier si vos informations d’identification sont correctes. Sélectionnez **Create** (Créer).

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="Créez un service lié à l’aide de la clé d’accès de stockage ADLS Gen2.":::


## <a name="readwrite-data-using-storage-account-name-and-key"></a>Lire/écrire des données à l’aide du nom et de la clé du compte de stockage

FSSPEC peut lire/écrire des données ADLS en spécifiant directement le nom et la clé du compte de stockage.

1. Dans Synapse studio, ouvrez **Données** > **Lié** > **Azure Data Lake Storage Gen2**. Chargez les données vers le compte de stockage par défaut.

1. Exécutez le code suivant.

   > [!NOTE]
   > Mettez à jour l’URL du fichier ainsi que le nom et la clé du compte de stockage ADLS Gen2 dans ce script avant de l’exécuter.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name
   adls_account_key = '' #Provide exact ADLS account key

   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name,    account_key=adls_account_key, mode="wt")
   
   with fsspec_handle.open() as f:
    data.to_csv(f)
   ```

## <a name="readwrite-data-using-linked-service"></a>Lire/écrire des données à l’aide du service lié

FSSPEC peut lire/écrire des données ADLS en spécifiant le nom du service lié.


1. Dans Synapse studio, ouvrez **Données** > **Lié** > **Azure Data Lake Storage Gen2**. Chargez les données vers le compte de stockage par défaut.

1. Exécutez le code suivant.

   > [!NOTE]
   > Mettez à jour l’URL du fichier, le nom du service lié ainsi que le nom du compte de stockage ADLS Gen2 dans ce script avant de l’exécuter.

   ```PYSPARK
   # To read data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>)
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)