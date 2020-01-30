---
title: Envoyer et recevoir des événements à l’aide de Python - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Python qui envoie des événements à Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: d7ab79d49aade7dd6e98cf33ce538174d176c784
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705341"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Envoyer ou recevoir des événements d’Event Hubs à l’aide de Python

Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce tutoriel montre comment créer des applications Python pour recevoir des événements à partir d’un hub d’événements ou lui en envoyer.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise la version 5 du Kit de développement logiciel (SDK) Azure Event Hubs Python. Pour un démarrage rapide qui utilise la version 1 antérieure du Kit de développement logiciel (SDK) Python, consultez [cet article](event-hubs-python-get-started-send.md). Si vous utilisez la version 1 du Kit de développement logiciel (SDK), nous vous recommandons de migrer votre code vers la dernière version. Pour plus d’informations, consultez le [guide de migration](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Un espace de noms Event Hubs et un Event Hub actifs, créés en suivant les instructions du [Démarrage rapide : Créer un hub d’événements avec le portail Azure](event-hubs-create.md). Prenez note des noms de l’espace de noms et d’Event Hub, car vous les utiliserez plus loin dans cette procédure pas à pas.
- Le nom de clé d’accès partagé et la valeur de clé primaire de votre espace de noms Event Hubs. Récupérez le nom et la valeur de la clé d’accès en suivant les instructions fournies dans [Obtention de la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Le nom de clé d’accès par défaut est **RootManageSharedAccessKey**. Copiez le nom de la clé et la valeur de clé primaire à utiliser plus loin dans cette procédure pas à pas.
- Python 2.7, 3.5 ou version ultérieure, avec `pip` installé et à jour.
- Le package Python pour Event Hubs. Pour installer le package, exécutez la commande suivante dans une invite de commandes dont le chemin contient Python :

    ```cmd
    pip install azure-eventhub
    ```

    Installez ce package pour recevoir les événements utilisant un stockage blob Azure comme magasin de points de contrôle.

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Envoyer des événements
Dans cette section, vous allez créer un script Python pour envoyer des événements au Event Hub que vous avez créé précédemment.

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un script appelé **send.py**. Ce script envoie un lot d’événements au Event Hub que vous avez créé précédemment.
3. Collez le code suivant dans send.py. Pour plus d’informations, consultez les commentaires du code.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Pour obtenir le code source complet avec des commentaires très utiles, consultez [ce fichier sur GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Recevoir des événements
Ce guide de démarrage rapide utilise un Stockage Blob Azure comme magasin de points de contrôle. Le magasin de points de contrôle est utilisé pour conserver les points de contrôle (dernière position de lecture).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Créer un compte Stockage Azure et un conteneur de blobs
Pour créer un compte Stockage Azure dans lequel se trouve un conteneur de blobs, procédez comme suit.

1. [Création d’un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. Créer un [conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenir la chaîne de connexion au compte de stockage](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Notez la chaîne de connexion et le nom du conteneur. Vous les utiliserez dans le code de réception.


### <a name="create-python-script-to-receive-events"></a>Créer un script Python pour recevoir des événements

Dans cette section, vous allez créer un script Python pour recevoir des événements de votre Event Hub :

1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code](https://code.visualstudio.com/).
2. Créez un script appelé **recv.py**.
3. Collez le code suivant dans recv.py. Pour plus d’informations, consultez les commentaires du code.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Pour obtenir le code source complet avec des commentaires très utiles, consultez [ce fichier sur GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Exécuter l’application réceptrice

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Exécuter l’application de l’expéditeur

Pour exécuter le script, ouvrez une invite de commandes qui a Python dans son chemin, puis exécutez cette commande :

```bash
python send.py
```

Vous devez voir les messages qui ont été envoyés au Event Hub dans la fenêtre du récepteur.


## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez envoyé et reçu des événements de manière asynchrone. Pour savoir comment envoyer et recevoir des événements de manière synchrone, consultez les exemples à [cet emplacement](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Vous trouverez tous les exemples (synchrone et asynchrone) sur la plateforme GitHub [ici](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
