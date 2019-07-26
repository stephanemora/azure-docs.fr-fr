---
title: Utiliser Python pour envoyer et recevoir des messages - Azure Event Hubs | Microsoft Docs
description: Découvrez comment envoyer ou recevoir des événements et capturer un flux d’événements avec Event Hubs à l’aide de Python.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: f86dc92b4df45119930970acfe9e173f32f894fb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356057"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Comment utiliser Azure Event Hubs à partir d’une application Python
Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour plus d’informations, consultez [Présentation d’Event Hubs](event-hubs-what-is-event-hubs.md). 

Cet article fournit des liens vers des articles qui vous montrent comment effectuer les tâches suivantes à partir d’une application écrite en **Python** :

- [Envoyer des événements à un hub d’événements](#send-events-to-event-hubs)
- [Recevoir des événements d’un hub d’événements](#receive-events-from-event-hubs)
- Lire des données d'événement capturées à partir d'un stockage Azure. 

## <a name="prerequisites"></a>Prérequis
- Créez un hub d’événements en suivant l’un de ces guides de démarrage rapide : [Portail Azure](event-hubs-create.md), [Azure CLI](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [modèle Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 ou ultérieur doit être installé sur votre ordinateur.

## <a name="install-python-package"></a>Installer le package Python

Pour installer le package Python pour Event Hubs, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande : 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Envoyer des événements vers Event Hubs
Le code suivant montre comment envoyer des événements à un hub d’événements à partir d’une application Python : 

1. Créez des variables pour contenir l’URL du hub d’événements, le nom de la clé et la valeur de clé. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Créez un client Event Hubs, ajoutez un expéditeur, exécutez le client, envoyez l’événement à l’aide de l’expéditeur, puis arrêtez le client une fois que vous avez terminé. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Pour un tutoriel complet sur l’envoi d’événements à un hub d’événements à partir d’une application écrite en Python, consultez [cet article](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Recevoir des événements d’Event Hubs
Le code suivant montre comment recevoir des événements d’un hub d’événements à partir d’une application Python : 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(
    CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Pour un tutoriel complet sur la réception d’événements d’un hub d’événements à partir d’une application écrite en Python, consultez [cet article](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>Lire des données d’événement capturées à partir d’un stockage Azure
Le code suivant vous montre comment lire les données d’événement capturées stockées dans un **stockage blob Azure** à partir d’une application Python : Activez la fonctionnalité **Capture** pour le hub d’événements en suivant les instructions dans : [Activer Event Hubs Capture à l’aide du portail Azure](event-hubs-capture-enable-through-portal.md). Ensuite, envoyez des événements au hub d’événements avant de tester le code. 

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
        if not dict.has_key(parsed_json['id']):
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
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
```

Pour un tutoriel complet sur la lecture de données Event Hubs capturées dans un stockage Blob Azure à partir d’une application écrite en Python, consultez [cet article](event-hubs-capture-python.md)

## <a name="github-samples"></a>Exemples GitHub
D’autres exemples Python sont disponibles dans le [dépôt Git azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>Étapes suivantes
Lisez les articles de la section Concepts en commençant par [Vue d’ensemble des fonctionnalités Event Hubs](event-hubs-features.md).
