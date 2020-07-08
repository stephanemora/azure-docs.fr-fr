---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant Python (ancienne version)
description: Cette procédure pas à pas vous montre comment créer et exécuter des scripts Python qui reçoivent des événements d’Azure Event Hubs ou lui en envoient à l’aide de l’ancien package azure-eventhub version 1.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 4ce53d2d82a00f98dbbd538bd7a61da9ba44e832
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314470"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Démarrage rapide : Envoyer et recevoir des événements avec Event Hubs en utilisant Python (azure-eventhub version 1)
Ce guide de démarrage rapide montre comment recevoir des événements d’un hub d’événements et lui en envoyer à l’aide du package Python **azure-eventhub version 1**. 

> [!WARNING]
> Ce guide de démarrage rapide utilise l’ancienne version du package azure-eventhub (version 1). Pour un guide de démarrage rapide qui utilise la dernière version du package (**version 5**), consultez [Envoyer et recevoir des événements à l’aide d’azure-eventhub version 5](get-started-python-send-v2.md). Pour migrer votre application de l’ancien package vers le nouveau, consultez [Guide to migrate from azure-eventhub version 1 to version 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Prérequis
Si vous débutez avec Azure Event Hubs, consultez la [vue d’ensemble d’Event Hubs](event-hubs-about.md) avant de suivre ce guide de démarrage rapide. 

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- **Abonnement Microsoft Azure**. Pour utiliser les services Azure, y compris Azure Event Hubs, vous avez besoin d’un abonnement.  Si vous n’avez pas de compte Azure, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/) ou utiliser les avantages de votre abonnement MSDN quand vous [créez un compte](https://azure.microsoft.com).
- Python 3.4 ou version ultérieure, avec `pip` installé et à jour.
- Le package Python pour Event Hubs. Pour installer le package, exécutez la commande suivante dans une invite de commandes dont le chemin contient Python : 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md). Ensuite, obtenez la valeur de la clé d’accès du hub d’événements en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utilisez la clé d’accès dans le code que vous écrivez plus loin dans ce guide de démarrage rapide. Le nom de clé par défaut est : **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Envoyer des événements

Pour créer une application Python qui envoie des événements à un hub d’événements :

> [!NOTE]
> Au lieu de passer par le démarrage rapide, vous pouvez télécharger et exécuter les [exemples d’application](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) à partir de GitHub. Remplacez les chaînes `EventHubConnectionString` et `EventHubName` par vos valeurs Event Hub.

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un nouveau fichier appelé *send.py*. Ce script envoie 100 événements à votre hub d’événements.
3. Collez le code suivant dans *send.py*, en remplaçant les Event Hubs \<namespace>, \<eventhub>, \<AccessKeyName>et \<primary key value> par vos valeurs : 
   
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
2. Collez le code suivant dans *recv.py*, en remplaçant les Event Hubs \<namespace>, \<eventhub>, \<AccessKeyName>et \<primary key value> par vos valeurs : 
   
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

