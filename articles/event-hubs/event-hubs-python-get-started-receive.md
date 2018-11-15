---
title: Recevoir des événements Azure Event Hubs avec Python | Microsoft Docs
description: Bien démarrer avec la réception d’événements Event Hubs avec Python
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: shvija
ms.openlocfilehash: db177c3356d378317c41e0c91c10865db5e05992
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286977"
---
# <a name="receive-events-from-event-hubs-using-python"></a>Recevoir des événements Event Hubs avec Python

Azure Event Hubs est un système de gestion des événements capable de traiter des millions d’événements par seconde, ce qui permet aux applications de traiter et d’analyser d’énormes quantités de données produites par des appareils connectés et autres applications. Une fois les événements collectés dans le hub d'événements, vous pouvez les recevoir et les gérer à l’aide de gestionnaires intra-processus ou par transfert vers d’autres systèmes d’analyse.

Pour en savoir plus sur Event Hubs, voir [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce tutoriel décrit comment recevoir des événements d’un hub d’événements à partir d’une application écrite en Python. Pour envoyer des événements, consultez [l’article correspondant sur l’envoi](event-hubs-python-get-started-send.md).

Le code utilisé dans ce didacticiel est extrait de ces [exemples GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) que vous pouvez examiner pour voir l’application pleinement fonctionnelle, avec des instructions d’importation et des déclarations de variable. D’autres exemples sont disponibles dans le même dossier GitHub.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Python 3.4 ou version ultérieure.
- Un espace de noms Event Hubs et un hub d’événements existants. Vous pouvez créer ces entités en suivant les instructions de [cet article](event-hubs-create.md). 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Installer le package Python

Pour installer le package Python pour Event Hubs, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande : 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-receive-events"></a>Créer un script Python pour recevoir des événements

Ensuite, créez une application Python qui reçoit des événements d’un hub d’événements :

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code][Visual Studio Code].
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
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
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

## <a name="receive-events"></a>Recevoir des événements

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé une application Python qui a reçu des messages d’un hub d’événements. Pour découvrir comment envoyer des événements à un hub d’événements à l’aide de Python, consultez [Envoyer des événements à un hub d’événements - Python](event-hubs-python-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
