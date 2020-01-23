---
title: Lire des données capturées par Azure Event Hubs à partir d’une application Python - | Microsoft Docs
description: Cet article explique comment écrire du code Python permettant de capturer les données envoyées à un hub d’événements, et comment lire les données d’événements capturées à partir du Stockage Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: d7a0c757f7314e45d5b4d13273df984739912b27
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942342"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Capturer des données Event Hubs dans le Stockage Azure et les lire à l’aide de Python 
Vous pouvez configurer un hub d’événements de sorte que les données qui lui sont envoyées soient capturées dans le Stockage Azure ou dans Azure Data Lake Storage. Cet article explique comment utiliser du code Python pour envoyer des événements au hub d’événements et lire les données capturées à partir du Stockage Blob Azure. Pour plus d’informations sur cette fonctionnalité, consultez la [présentation de la fonctionnalité Event Hubs Capture](event-hubs-capture-overview.md).

Cet exemple utilise le [Kit de développement logiciel (SDK) Azure Python](https://azure.microsoft.com/develop/python/) pour illustrer la fonctionnalité Capture. Le programme sender.py envoie la télémétrie de l’environnement simulé à Event Hubs au format JSON. Le hub d’événements est configuré pour utiliser la fonctionnalité Capture afin d’écrire ces données dans le stockage Blob en lots. L’application capturereader.py lit ces objets blob et crée un fichier Append par appareil. Ensuite, elle écrit les données dans des fichiers .csv.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise la version 5 du Kit de développement logiciel (SDK) Azure Event Hubs Python. Pour un démarrage rapide qui utilise la version 1 antérieure du Kit de développement logiciel (SDK) Python, consultez [cet article](event-hubs-capture-python.md). Si vous utilisez la version 1 du Kit de développement logiciel (SDK), nous vous recommandons de migrer votre code vers la dernière version. Pour plus d’informations, consultez le [guide de migration](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

Dans ce guide de démarrage rapide, vous allez : 

> [!div class="checklist"]
> * Créer un compte de stockage d’objets Blob Azure et un conteneur dans le portail Azure.
> * Créer un espace de noms Event Hubs à l’aide du portail Azure
> * Créer un hub d’événements avec la fonctionnalité de capture activée, puis le connecter à votre compte de stockage
> * Envoyer des données à votre Event Hub à l’aide d’un script Python.
> * Lire et traiter les fichiers d’Event Hubs Capture à l’aide d’un autre script Python.

## <a name="prerequisites"></a>Conditions préalables requises

- Python 2.7, 3.5 ou version ultérieure, avec `pip` installé et à jour.
- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- [Créer un espace de noms Event Hubs comprenant un hub d’événement](event-hubs-create.md). Notez le nom de l’espace de noms Event Hubs, le nom du hub d’événements et la clé d’accès primaire de l’espace de noms. Obtenez la clé d’accès en suivant les instructions de cet article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Le nom de clé par défaut est : **RootManageSharedAccessKey**. Vous n’aurez pas besoin de la chaîne de connexion pour ce tutoriel. Vous avez seulement besoin de la clé primaire. 
- Effectuez les étapes suivantes pour créer un **compte de stockage Azure** et un **conteneur d’objets blob** :
    1. [Créer un compte de stockage Azure](/storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
    2. [Créer un conteneur d’objets blob dans le stockage](/storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 
    3. [Obtenir la chaîne de connexion au compte de stockage](/storage/common/storage-configure-connection-string?#view-and-copy-a-connection-string)

        Notez la **chaîne de connexion** et le **nom du conteneur**. Vous les utiliserez plus tard dans le code. 
- Activez la fonctionnalité **Capture** pour le hub d’événements en suivant les instructions ci-après : [Activer Event Hubs Capture à l’aide du portail Azure](event-hubs-capture-enable-through-portal.md). Sélectionnez le compte de stockage et le conteneur d’objets blob que vous avez créés à l’étape précédente. Vous pouvez également activer la fonctionnalité lors de la création d’un hub d’événements. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Créer un script Python pour envoyer des événements à votre concentrateur d’événements
Dans cette section, vous allez créer un script Python qui envoie 200 événements (10 appareils * 20 événements) à un hub d’événements. Ces événements sont des exemples de relevés environnementaux qui sont envoyés au format JSON. 

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code][Visual Studio Code].
2. Créez un script appelé **sender.py**. 
3. Collez le code suivant dans sender.py. Pour plus d’informations, consultez les commentaires du code. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Remplacez les valeurs suivantes dans le script :
    1. Remplacez `EVENT HUBS NAMESPACE CONNECTION STRING` par la chaîne de connexion de votre espace de noms Event Hubs.
    2. Remplacez `EVENT HUB NAME` par le nom de votre hub d’événements. 
5. Exécutez le script pour envoyer des événements au hub d’événements. 
6. Dans le portail Azure, vous pouvez vérifier que le hub d’événements a reçu les messages. Basculez vers l’affichage **Messages** dans la section **Métriques**. Actualisez la page pour mettre à jour le graphique. Cela peut prendre quelques secondes pour indiquer que les messages ont été reçus. 

    [![Vérifier que le hub d’événements a reçu les messages](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Créer un script Python pour lire vos fichiers Capture
Dans cet exemple, les données capturées sont stockées dans le Stockage Blob Azure. Le script de cette section lit les fichiers de données capturées à partir du Stockage Azure et génère des fichiers CSV qui vous permettent d’ouvrir et d’afficher facilement leur contenu. Vous verrez que le répertoire de travail actuel de l’application contient 10 fichiers. Ces fichiers contiennent les données environnementales qui ont été relevées par les 10 appareils. 

1. Dans votre éditeur Python, créez un script que vous nommerez **capturereader.py**. Ce script lit les fichiers capturés et crée un fichier par appareil pour écrire les données uniquement pour cet appareil.
2. Collez le code suivant dans capturereader.py. Pour plus d’informations, consultez les commentaires du code. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Remplacez `<AZURE STORAGE CONNECTION STRING>` par la chaîne de connexion de votre compte de stockage Azure. Le nom du conteneur que vous avez créé dans ce tutoriel est le suivant : **capture**. Si vous avez utilisé un autre nom pour le conteneur, remplacez `capture` par le nom du conteneur qui se trouve dans le compte de stockage. 

## <a name="run-the-scripts"></a>Exécuter les scripts
1. Ouvrez une invite de commandes comprenant Python dans son chemin d’accès, puis exécutez ces commandes pour installer les packages de configuration requise Python :
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Remplacez votre répertoire par celui dans lequel vous avez enregistré sender.py et capturereader.py, puis exécutez cette commande :
   
   ```
   python sender.py
   ```
   
   Cette commande démarre un nouveau processus Python pour exécuter l’expéditeur.
3. Attendez quelques minutes pour que la capture s’exécute. Puis tapez la commande suivante dans la fenêtre de commande d’origine :
   
   ```
   python capturereader.py
   ```

   Ce processeur de capture utilise le répertoire local pour télécharger tous les objets blob à partir du compte de stockage / conteneur. Il traite tous ceux qui ne sont pas vides et écrit les résultats sous la forme de fichiers .csv dans le répertoire local.

## <a name="next-steps"></a>Étapes suivantes
Pour voir les exemples Python sur GitHub, [cliquez ici](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
