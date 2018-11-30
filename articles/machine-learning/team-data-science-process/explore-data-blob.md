---
title: Explorer les données dans le stockage d’objets blob Azure avec Pandas | Microsoft Docs
description: Comment explorer les données stockées dans un conteneur d’objets blob Azure à l’aide de Pandas.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 12e277168d27786581d14adc4c32b94d0979df6d
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441578"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Explorer les données dans le stockage d’objets blob Azure avec Pandas

Cet article explique comment explorer les données stockées dans un conteneur d’objets blob Azure à l’aide du package Python [pandas](http://pandas.pydata.org/).

Cette tâche est une étape du [processus Team Data Science Process](overview.md).

## <a name="prerequisites"></a>Prérequis
Cet article suppose que vous avez :

* Créé un compte de stockage Azure. Pour des instructions, voir [Créer un compte Stockage Azure](../../storage/common/storage-quickstart-create-account.md).
* Stocké vos données dans un compte de stockage d’objets blob Azure. Si vous avez besoin d’aide, consultez la page [Transfert de données vers et à partir d’Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Chargement des données dans une trame de données Pandas
Pour explorer et manipuler un jeu de données, celui-ci doit d’abord être téléchargé depuis la source Blob vers un fichier local qui peut ensuite être chargé dans une trame de données Pandas. Voici les étapes à suivre pour cette procédure :

1. Téléchargez les données à partir du blob Azure avec l’exemple de code Python à l’aide du service BLOB. Remplacez la variable dans le code ci-dessous par vos propres valeurs : 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Lisez les données du fichier téléchargé dans une table Pandas.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Vous êtes maintenant prêt à explorer les données et à générer des fonctionnalités sur cet ensemble de données.

## <a name="blob-dataexploration"></a>Exemples d’exploration de données à l’aide de Pandas
Voici quelques méthodes pour explorer des données à l’aide de Pandas :

1. Vérifiez le **nombre de lignes et de colonnes** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Vérifiez** les premières ou dernières **lignes** de l’ensemble de données ci-dessous :
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Vérifiez le **type de données** dans lequel chaque colonne a été importée, à l’aide du code suivant :
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Vérifiez les **statistiques de base** des colonnes dans l’ensemble de données, comme indiqué ci-dessous :
   
        dataframe_blobdata.describe()
5. Regardez le nombre d’entrées pour chaque valeur de colonne, comme indiqué ci-dessous :
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **valeurs manquantes** par rapport au nombre réel d’entrées dans chaque colonne, à l’aide du code suivant :
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Si des **valeurs sont manquantes** dans une colonne spécifique, vous pouvez les supprimer comme suit :
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   L’autre solution pour remplacer les valeurs manquantes consiste à utiliser la fonction mode :
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<nom_colonne>':dataframe_blobdata['<nom_colonne>'].mode()[0]})        
8. Créez un **histogramme** à l’aide d’un nombre variable de compartiments pour tracer la distribution d’une variable :    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Examinez les **corrélations** entre les variables à l’aide d’un nuage de points ou de la fonction de corrélation intégrée :
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

