---
title: Échantillonner des données dans le stockage d’objets blob Azure - Processus TDSP (Team Data Science Process)
description: Échantillonnage des données conservées dans le stockage d’objets blob Azure par le biais du téléchargement de ces données par programmation, puis de leur échantillonnage à l’aide de procédures écrites dans Python.
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
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720277"
---
# <a name="heading"></a>Échantillonner des données dans le stockage d’objets blob Azure

Cet article traite de l’échantillonnage des données conservées dans le stockage d’objets blob Azure par le biais du téléchargement de ces données par programmation, puis de leur échantillonnage à l’aide de procédures écrites dans Python.

**Pourquoi échantillonner vos données ?**
Si vous prévoyez d’analyser un jeu de données volumineux, il est généralement recommandé de sous-échantillonner les données afin de réduire leur taille sous une forme plus facilement exploitable, mais toujours représentative. L’échantillonnage facilite la compréhension des données, l’exploration ainsi que l’ingénierie de fonctionnalités. Son rôle dans le processus Cortana Analytics consiste à permettre le prototypage rapide des fonctions de traitement des données et des modèles d’apprentissage automatique.

Cette tâche d’échantillonnage est une étape du [processus TDSP (Team Data Science Process)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Télécharger et sous-échantillonner les données
1. Téléchargez les données du stockage d’objets blob Azure à l’aide du service BLOB en utilisant l’exemple de code Python suivant : 
   
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

2. Lisez les données dans une trame de données pandas à partir du fichier téléchargé à l’étape précédente.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Sous-échantillonnez les données en utilisant l’élément `random.choice` de `numpy`, comme suit :
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Vous pouvez à présent travailler sur l’échantillon de 1 % de la trame de données ci-dessus à d’autres fins d’exploration et de génération de fonctionnalités.

## <a name="heading"></a>Télécharger les données et les lire dans Azure Machine Learning
Vous pouvez sous-échantillonner les données et les utiliser directement dans Azure Machine Learning en utilisant l’exemple de code ci-dessous :

1. Écrivez la trame de données dans un fichier local :
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Chargez le fichier local dans un objet blob Azure au moyen de l’exemple de code suivant :
   
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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Lisez les données de l’objet blob Azure à l’aide du module [Importer les données](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) Azure Machine Learning, comme l’illustre l’image ci-dessous :

![objet blob de lecteur](./media/sample-data-blob/reader_blob.png)

