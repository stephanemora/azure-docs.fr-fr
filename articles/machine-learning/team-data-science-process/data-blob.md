---
title: Traiter des données d’objets blob Azure avec une analytique avancée - Team Data Science Process
description: Explorez des données et générez des caractéristiques à partir de données stockées dans Stockage Blob Azure avec une analytique avancée.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4c47dfb8b221b6cb4b6237669ecd17c1637107a2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721096"
---
# <a name="heading"></a>Traitement des données d’objets blob Azure avec des analyses de données avancées
Ce document concerne l’exploration des données et la génération de fonctionnalités à partir de données stockées dans le stockage d’objets blob. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Chargement des données dans une trame de données Pandas
Pour qu’il soit possible d’explorer et de manipuler un jeu de données, celui-ci doit être téléchargé de la source Blob vers un fichier local qui peut ensuite être chargé dans une trame de données Pandas. Voici les étapes à suivre pour cette procédure :

1. Téléchargez les données à partir de l’objet blob Azure avec l’exemple suivant de code Python utilisant le service BLOB. Remplacez la variable dans le code ci-dessous par vos propres valeurs : 
   
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

## <a name="blob-dataexploration"></a>Exploration des données
Voici quelques méthodes pour explorer des données à l’aide de Pandas :

1. Vérifiez le nombre de lignes et de colonnes. 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Vérifiez les premières ou dernières lignes de l’ensemble de données, comme indiqué ci-dessous :
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Vérifiez le type de données dans lequel chaque colonne a été importée, à l’aide du code suivant :
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Vérifiez les statistiques de base des colonnes dans l’ensemble de données, comme indiqué ci-dessous :
   
        dataframe_blobdata.describe()
5. Regardez le nombre d’entrées pour chaque valeur de colonne, comme indiqué ci-dessous :
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Comptez les valeurs manquantes par rapport au nombre réel d’entrées dans chaque colonne, à l’aide du code suivant :
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Si des valeurs sont manquantes dans une colonne spécifique, vous pouvez les supprimer comme suit :
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   L’autre solution pour remplacer les valeurs manquantes consiste à utiliser la fonction mode :
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Créez un histogramme à l’aide d’un nombre variable de compartiments pour tracer la distribution d’une variable :    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Examinez les corrélations entre les variables à l’aide d’un nuage de points ou de la fonction de corrélation intégrée :
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Génération de fonctionnalités
Pour générer des caractéristiques à l’aide de Python, procédez comme suit :

### <a name="blob-countfeature"></a>Génération de caractéristiques à partir de valeurs d’indicateur
Pour créer des caractéristiques de catégorie, procédez comme suit :

1. Examinez la distribution de la colonne de catégorie :
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Générez les valeurs d’indicateur pour chacune des valeurs de colonne :
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Créez une jointure entre la colonne d’indicateurs et le bloc de données d’origine : 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Supprimez la variable d’origine :
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Génération de caractéristiques de compartimentage
Pour générer des fonctionnalités compartimentées, procédez comme suit :

1. Ajoutez une séquence de colonnes pour compartimenter une colonne numérique :
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Convertissez le compartimentage en une séquence de variables booléennes :
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Enfin, créez une jointure entre les variables factices et le bloc de données d’origine :
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Réécriture de données dans l’objet blob Azure et exploitation dans Azure Machine Learning
Après avoir exploré les données et créé les fonctionnalités nécessaires, vous pouvez charger les données (échantillonnées ou caractérisées) dans un objet blob Azure et les utiliser dans Azure Machine Learning en effectuant les étapes suivantes : Vous pouvez aussi créer d’autres fonctionnalités dans Azure Machine Learning Studio (classique). 

1. Écrivez le bloc de données dans le fichier local.
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Chargez les données dans le blob Azure, en procédant comme suit :
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. À présent, les données sont lisibles à partir de l’objet blob à l’aide du module [Importer des données][import-data] d’Azure Machine Learning comme le montre l’écran ci-dessous :

![objet blob de lecteur][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

