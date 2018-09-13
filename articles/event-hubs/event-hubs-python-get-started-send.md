---
title: Envoyer des événements à Azure Event Hubs avec Python | Microsoft Docs
description: Bien démarrer avec l’envoi d’événements à Azure Event Hubs avec Python
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703408"
---
# <a name="send-events-to-event-hubs-using-python"></a>Envoyer des événements à Event Hubs avec Python

Azure Event Hubs est un système de gestion des événements capable de traiter des millions d’événements par seconde, ce qui permet aux applications de traiter et d’analyser d’énormes quantités de données produites par des appareils connectés et autres applications. Une fois les événements collectés dans le hub d'événements, vous pouvez les recevoir et les gérer à l’aide de gestionnaires intra-processus ou par transfert vers d’autres systèmes d’analyse.

Pour en savoir plus sur Event Hubs, voir [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce tutoriel décrit comment envoyer des événements à un hub d’événements à partir d’une application écrite en Python. Pour recevoir des événements, consultez [l’article correspondant sur la réception](event-hubs-python-get-started-receive.md).

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

## <a name="create-a-python-script-to-send-events"></a>Créer un script Python pour recevoir des événements

Ensuite, créez une application Python qui envoie des événements à un hub d’événements :

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code][Visual Studio Code].
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
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

## <a name="send-events"></a>Envoyer des événements

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
start python send.py
```
 
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez envoyé des événements à un hub d’événements avec Python, consultez [l’article correspondant sur la réception](event-hubs-python-get-started-receive.md) pour recevoir des événements.

Pour plus d’informations sur Event Hubs, visitez les pages suivantes :

* [Vue d’ensemble des hubs d’événements][Event Hubs overview]
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
