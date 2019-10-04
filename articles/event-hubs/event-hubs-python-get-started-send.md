---
title: Envoyer et recevoir des événements à l’aide de Python - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Python qui envoie des événements à Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/16/2019
ms.author: shvija
ms.openlocfilehash: 5162c6359c4b6e6bdd53d2778ca247704e2f16be
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059140"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Envoyer ou recevoir des événements d’Event Hubs à l’aide de Python

Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce tutoriel montre comment créer des applications Python pour recevoir des événements à partir d’un hub d’événements ou lui en envoyer. 

> [!NOTE]
> Vous pouvez télécharger ce guide de démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), remplacer les chaînes `EventHubConnectionString` et `EventHubName` par les valeurs de votre hub d’événements, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Python 3.4 ou version ultérieure.
- Utilisez le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenez les informations de gestion nécessaires à votre application pour communiquer avec le hub d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md). Ensuite, obtenez la valeur de la clé d’accès du hub d’événements en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utilisez la clé d’accès dans le code que vous écrivez plus loin dans ce tutoriel. Le nom de clé par défaut est : **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Installer le package Python

Pour installer le package Python pour Event Hubs, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande : 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Envoyer des événements

> [!NOTE]
> Le code dans cette section est destiné à la version stable actuelle (1.3.1) du Kit de développement logiciel (SDK) Event Hubs. Si vous recherchez l’exemple de code qui utilise la préversion du Kit de développement logiciel (SDK), consultez [cette page](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

### <a name="create-a-python-script-to-send-events"></a>Créer un script Python pour recevoir des événements

Ensuite, créez une application Python qui envoie des événements à un hub d’événements :

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un script appelé **send.py**. Ce script envoie 100 événements à votre hub d’événements.
3. Collez le code suivant dans send.py, en remplaçant les valeurs de ADDRESS, USER et KEY par celles obtenues dans le portail Azure dans la section précédente : 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://<EVENTHUBS NAMESPACE NAME>.servicebus.windows.net/<EVENTHUB NAME>"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "<SHARED ACCESS KEY FOR THE EVENT HUBS NAMESPACE>"

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

### <a name="run-application-to-send-events"></a>Exécuter l’application pour envoyer des événements

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
start python send.py
```

Félicitations ! Vous venez d’envoyer des messages à un concentrateur d’événements.

## <a name="receive-events"></a>Recevoir des événements

### <a name="create-a-python-script-to-receive-events"></a>Créer un script Python pour recevoir des événements

Ensuite, créez une application Python qui reçoit des événements d’un hub d’événements :

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un script appelé **recv.py**.
3. Collez le code suivant dans recv.py, en remplaçant les valeurs de ADDRESS, USER et KEY par celles obtenues dans le portail Azure dans la section précédente : 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://<EVENTHUBS NAMESPACE NAME>.servicebus.windows.net/<EVENTHUB NAME>"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "<SHARED ACCESS KEY FOR THE EVENT HUBS NAMESPACE>"

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

### <a name="receive-events"></a>Recevoir des événements

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Fonctionnalités et terminologie dans Azure Event Hubs](event-hubs-features.md)
- [FAQ sur les hubs d’événements](event-hubs-faq.md)

