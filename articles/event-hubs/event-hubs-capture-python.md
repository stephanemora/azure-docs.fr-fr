---
title: 'Démarrage rapide : Lire des données capturées à partir d’une application Python – Azure Event Hubs'
description: 'Démarrage rapide : Des scripts qui utilisent le Kit de développement logiciel (SDK) Azure Python pour illustrer la fonctionnalité Event Hubs Capture.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18, tracking-python
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: c726b0d11759d30730046e635c701cf23d130dfc
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561565"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Démarrage rapide : Procédure pas à pas d’Event Hubs Capture : Python (azure-eventhub version 1)

Capture est une fonctionnalité d’Azure Event Hubs. Vous pouvez utiliser Capture pour fournir automatiquement les données de streaming de votre hub d’événements à un compte de stockage Blob Azure de votre choix. Cette fonctionnalité facilite le traitement par lots des données de flux en temps réel. Cet article décrit comment utiliser Event Hubs Capture avec Python. Pour plus d’informations sur Event Hubs Capture, consultez [Capturer des événements avec Azure Event Hubs][Overview of Event Hubs Capture].

Cette procédure pas à pas utilise le [Kit de développement logiciel (SDK) Azure Python](https://azure.microsoft.com/develop/python/) pour illustrer la fonctionnalité Capture. Le programme *sender.py* envoie la télémétrie de l’environnement simulé à Event Hubs au format JSON. Le hub d’événements utilise la fonctionnalité Capture afin d’écrire ces données dans le stockage Blob en lots. L’application *capturereader.py* lit ces objets Blob, crée un fichier Append pour chacun de vos appareils et écrit les données dans des fichiers *.csv* sur chaque appareil.

> [!WARNING]
> Ce guide de démarrage rapide concerne la version 1 du SDK Azure Event Hubs Python. Nous vous recommandons de [migrer](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) votre code vers la [version 5 du SDK Python](get-started-capture-python-v2.md).

Lors de cette procédure pas à pas, vous allez effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Créer un compte de stockage d’objets Blob Azure et un conteneur dans le portail Azure.
> * Activer Event Hubs Capture et diriger la sortie vers votre compte de stockage.
> * Envoyer des données à votre Event Hub à l’aide d’un script Python.
> * Lire et traiter les fichiers d’Event Hubs Capture à l’aide d’un autre script Python.

## <a name="prerequisites"></a>Prérequis

- Python 3.4 ou version ultérieure, avec `pip` installé et à jour.
  
- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
  
- Un espace de noms Event Hubs et un Event Hub actifs, créés en suivant les instructions du [Démarrage rapide : Créer un hub d’événements avec le portail Azure](event-hubs-create.md). Prenez note des noms de votre espace de noms et de votre Event Hub, car vous les utiliserez plus loin dans cette procédure pas à pas. 
  
  > [!NOTE]
  > Si vous disposez déjà d’un conteneur de stockage à utiliser, vous pouvez activer Capture et sélectionner le conteneur de stockage lorsque vous créez votre Event Hub. 
  > 
  
- Votre nom de clé d’accès partagé et la valeur de clé primaire de votre Event Hubs. Recherchez ou créez ces valeurs sous **Stratégies d’accès partagé** sur votre page Event Hubs. Le nom de clé d’accès par défaut est **RootManageSharedAccessKey**. Copiez le nom de la clé d’accès et la valeur de clé primaire à utiliser plus loin dans cette procédure pas à pas. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Créer un compte de stockage Blob Azure et un conteneur

Créez un compte de stockage et un conteneur à utiliser pour la capture. 

1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le volet de navigation de gauche, sélectionnez **Comptes de stockage**, puis dans l’écran **Comptes de stockage**, sélectionnez **Ajouter**.
3. Dans l’écran de création du compte de stockage, sélectionnez un abonnement et un groupe de ressources, puis attribuez un nom au compte de stockage. Vous pouvez conserver les autres sélections par défaut. Sélectionnez **Vérifier + Créer**, vérifiez les paramètres, puis sélectionnez **Créer**. 
   
   ![Créer un compte de stockage][1]
   
4. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**, et sur l’écran **Vue d’ensemble** du compte de stockage, sélectionnez **Conteneurs**.
5. Dans l’écran **Conteneurs**, sélectionnez **+ conteneur**. 
6. Dans l’écran **Nouveau conteneur**, attribuez un nom au conteneur, puis sélectionnez **OK**. Notez le nom du conteneur, que vous utiliserez ultérieurement dans la procédure pas à pas. 
7. Dans le volet de navigation de gauche de l’écran **Conteneurs**, sélectionnez **Clés d’accès**. Copiez le **Nom du compte de stockage** et la valeur **Clé** sous **key1**, que vous utiliserez ultérieurement dans la procédure pas à pas.
 
## <a name="enable-event-hubs-capture"></a>Activer Event Hubs Capture

1. Dans le portail Azure, accédez à votre Event Hub en sélectionnant son espace de noms Event Hubs dans **Toutes les ressources**, en sélectionnant **Event Hubs** dans le volet de navigation de gauche, puis en sélectionnant votre Event Hub. 
2. Dans l’écran **Vue d’ensemble** de votre Event Hub, sélectionnez **Capturer des événements**.
3. Dans l’écran **Capture**, sélectionnez **Activé**. Ensuite, sous **Conteneur de stockage Azure**, sélectionnez **Sélectionner un conteneur**. 
4. Sur l’écran **Conteneurs**, sélectionnez le conteneur de stockage que vous souhaitez utiliser, puis **Sélectionner**. 
5. Dans l’écran **Capture** , sélectionnez **Enregistrer les modifications**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Créer un script Python pour envoyer des événements à Event Hub
Ce script envoie 200 événements à votre concentrateur d’événements. Ces événements sont de simples lectures environnementales envoyées au format JSON.

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code][Visual Studio Code].
2. Créez un nouveau fichier appelé *sender.py*. 
3. Collez le code suivant dans *sender.py*. Indiquez vos propres valeurs pour les Event Hubs \<namespace>, \<AccessKeyName>, \<primary key value> et \<eventhub>.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Enregistrez le fichier .

## <a name="create-a-python-script-to-read-capture-files"></a>Créer un script Python pour lire les fichiers Capture

Ce script lit les fichiers capturés et crée un fichier pour chaque appareil afin d’écrire les données uniquement pour cet appareil.

1. Dans votre éditeur Python, créez un nouveau fichier appelé *capturereader.py*. 
2. Collez le code suivant dans *capturereader.py*. Indiquez vos valeurs enregistrées pour \<storageaccount>, \<storage account access key> et \<storagecontainer>.
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
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
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Exécuter les scripts Python

1. Ouvrez une invite de commandes comprenant Python dans son chemin d’accès, puis exécutez ces commandes pour installer les packages de configuration requise Python :
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Si vous disposez d’une version antérieure de `azure-storage` ou `azure`, vous devrez peut-être utiliser l’option `--upgrade`.
   
   Vous devrez peut-être également exécuter la commande suivante. L’exécution de cette commande n’est pas nécessaire sur la plupart des systèmes. 
   
   ```cmd
   pip install cryptography
   ```
   
2. À partir du répertoire où vous avez enregistré *sender.py* et *capturereader.py* , exécutez la commande suivante :
   
   ```cmd
   start python sender.py
   ```
   
   La commande démarre un nouveau processus Python pour exécuter l’expéditeur.
   
3. Une fois l’exécution de la capture terminée, exécutez la commande suivante :
   
   ```cmd
   python capturereader.py
   ```

   Le processeur de capture télécharge tous les objets Blob non vides à partir du conteneur de compte de stockage et écrit les résultats sous la forme de fichiers *.csv* dans le répertoire local. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Event Hubs, consultez : 

* [Présentation d’Event Hubs Capture][Overview of Event Hubs Capture]
* [Exemples d’application complets qui utilisent des concentrateurs d’événements](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Vue d’ensemble d’Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
