---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant Python (ancienne version)
description: Cette procédure pas à pas vous montre comment créer et exécuter des scripts Python qui reçoivent des événements d’Azure Event Hubs ou lui en envoient à l’aide de l’ancien package azure-eventhub version 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 654ccd6352dc0b671cc3becdafd2f1e1102dd39e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902940"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Démarrage rapide : Envoyer et recevoir des événements avec Event Hubs en utilisant Python (azure-eventhub version 1)

Azure Event Hubs est une plateforme de streaming Big Data et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie de logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour plus d’informations sur Event Hubs, consultez [Azure Event Hubs](event-hubs-about.md) et [Fonctionnalités et terminologie dans Azure Event Hubs](event-hubs-features.md).

Ce démarrage rapide montre comment créer des applications Python pour recevoir des événements à partir d’un hub d’événements et lui en envoyer. 

> [!WARNING]
> Ce guide de démarrage rapide concerne la version 1 du SDK Azure Event Hubs Python. Nous vous recommandons de [migrer](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) votre code vers la [version 5 du SDK Python](get-started-python-send-v2.md).

 
 

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Un espace de noms Event Hubs et un Event Hub actifs, créés en suivant les instructions du [Démarrage rapide : Créer un hub d’événements avec le portail Azure](event-hubs-create.md). Prenez note des noms de l’espace de noms et d’Event Hub, car vous les utiliserez plus loin dans cette procédure pas à pas. 
- Le nom de clé d’accès partagé et la valeur de clé primaire de votre espace de noms Event Hubs. Récupérez le nom et la valeur de la clé d’accès en suivant les instructions fournies dans [Obtention de la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Le nom de clé d’accès par défaut est **RootManageSharedAccessKey**. Copiez le nom de la clé et la valeur de clé primaire à utiliser plus loin dans cette procédure pas à pas. 
- Python 3.4 ou version ultérieure, avec `pip` installé et à jour.
- Le package Python pour Event Hubs. Pour installer le package, exécutez la commande suivante dans une invite de commandes dont le chemin contient Python : 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```


## <a name="send-events"></a>Envoyer des événements

Pour créer une application Python qui envoie des événements à un hub d’événements :

> [!NOTE]
> Au lieu de passer par le démarrage rapide, vous pouvez télécharger et exécuter les [exemples d’application](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) à partir de GitHub. Remplacez les chaînes `EventHubConnectionString` et `EventHubName` par vos valeurs Event Hub.

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un nouveau fichier appelé *send.py*. Ce script envoie 100 événements à votre hub d’événements.
3. Collez le code suivant dans *send.py*, en remplaçant \<namespace>, \<eventhub>, \<AccessKeyName> et \<primary key value> par vos valeurs d’Event Hubs : 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Enregistrez le fichier . 

Pour exécuter le script, exécutez la commande suivante à partir du répertoire où vous avez enregistré *send.py* :

```cmd
start python send.py
```

Félicitations ! Vous venez d’envoyer des messages à un concentrateur d’événements.

## <a name="receive-events"></a>Recevoir des événements

Pour créer une application Python qui reçoit des événements d’un hub d’événements :

1. Dans votre éditeur Python, créez un fichier appelé *recv.py*.
2. Collez le code suivant dans *recv.py*, en remplaçant \<namespace>, \<eventhub>, \<AccessKeyName> et \<primary key value> par vos valeurs d’Event Hubs : 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Enregistrez le fichier .

Pour exécuter le script, exécutez la commande suivante à partir du répertoire où vous avez enregistré *recv.py* :

```cmd
start python recv.py
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur Event Hubs, consultez les articles suivants :

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Fonctionnalités et terminologie dans Azure Event Hubs](event-hubs-features.md)
- [FAQ sur les hubs d’événements](event-hubs-faq.md)

