---
title: Recevoir des événements à l’aide de Java - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Java qui reçoit des événements d’Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 0fc9b8b6a8bcd62aafda7c04697ab8b9c096b17e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296577"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Recevoir des événements d’Azure Event Hubs avec Java

Les concentrateurs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la section [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce didacticiel explique comment recevoir des événements dans un concentrateur d’événements à l’aide d’une application console en Java.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un environnement de développement Java. Pour ce didacticiel, nous partons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).
* Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.

Le code de ce didacticiel est basé sur le [code EventProcessorSample sur GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), que vous pouvez analyser pour afficher la version complète de l’application en cours.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Recevoir des messages avec EventProcessorHost en Java

**EventProcessorHost** est une classe Java qui simplifie la réception d’événements provenant d’Event Hubs grâce à la gestion des points de contrôle permanents et des réceptions en parallèle d’Event Hubs. EventProcessorHost permet de répartir des événements sur plusieurs récepteurs, même quand ils sont hébergés dans des nœuds différents. Cet exemple illustre l'utilisation de la classe EventProcessorHost pour un récepteur unique.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Pour utiliser EventProcessorHost, vous devez disposer d’un [compte Azure Storage][Azure Storage account] :

1. Connectez-vous au [Portail Azure][Azure portal], puis cliquez sur **+ Créer une ressource** à gauche de l’écran.
2. Cliquez sur **Stockage**, puis sur **Compte de stockage**. Dans la fenêtre **Créer un compte de stockage**, saisissez un nom pour votre compte de stockage. Renseignez les autres champs, sélectionnez la région souhaitée, puis cliquez sur **Créer**.
   
    ![Créer un compte de stockage](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Cliquez sur le compte de stockage que vous venez de créer, puis cliquez sur **Clés d'accès** :
   
    ![Obtenir les clés d’accès](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Copiez la valeur key1 dans un emplacement temporaire. Vous l’utiliserez ultérieurement dans ce tutoriel.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Créer un projet Java à l’aide de l’hôte EventProcessor

La bibliothèque cliente Java pour Event Hubs est utilisable dans les projets Maven à partir du [référentiel central Maven][Maven Package]. Elle peut être référencée à l’aide de la déclaration de dépendance ci-après dans votre fichier projet Maven : 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Pour différents types d’environnements build, vous pouvez obtenir explicitement les fichiers JAR les plus récents à partir du [Référentiel central Maven][Maven Package].  

1. Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. La classe est appelée `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Utilisez le code suivant pour créer une classe appelée `EventProcessorSample`. Remplacez les espaces réservés par les valeurs utilisées lorsque vous avez créé le concentrateur d’événements et le compte de stockage :
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Créez une autre classe appelée `EventProcessor` à l’aide du code suivant :
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Ce didacticiel utilise une seule instance de EventProcessorHost. Pour augmenter le débit, nous recommandons d’exécuter plusieurs instances d’EventProcessorHost, de préférence sur des machines séparées.  Cela offre également davantage de redondance. Dans ces cas, les différentes instances se coordonnent automatiquement entre elles afin d'équilibrer la charge des événements reçus. Si vous souhaitez que plusieurs récepteurs traitent *tous* les événements, vous devez utiliser le concept **ConsumerGroup** . Lors de la réception des événements à partir de différents ordinateurs, il peut être utile de spécifier des noms pour les instances de EventProcessorHost basées sur les ordinateurs (ou rôles) dans lesquels ils sont déployés.

## <a name="publishing-messages-to-eventhub"></a>Publication de messages sur EventHub

Avant que les messages ne soient récupérés par les consommateurs, ils doivent d’abord être publiés sur les partitions par les éditeurs. Il est important de noter que lorsque les messages sont publiés sur Event Hub de façon synchrone à l’aide de la méthode sendSync() sur l’objet com.microsoft.azure.eventhubs.EventHubClient, le message peut être envoyé à une partition spécifique ou distribué à toutes les partitions disponibles par tourniquet (round robin) selon que la clé de partition est spécifiée ou non.

Lorsqu’une chaîne représentant la clé de partition est spécifiée, la clé est hachée pour déterminer à quelle partition envoyer l’événement.

Lorsque la clé de partition n’est pas définie, les messages sont distribués par tourniquet (round robin) à toutes les partitions disponibles

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

## <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Mise en œuvre d’un CheckpointManager personnalisé pour EventProcessorHost (EPH)

L’API fournit un mécanisme pour implémenter votre gestionnaire de point de contrôle personnalisé pour les scénarios où la mise en œuvre par défaut n’est pas compatible avec votre cas d’utilisation.

Le gestionnaire de point de contrôle par défaut utilise le stockage blob, mais si vous remplacez le gestionnaire de point de contrôle utilisé par EPH par votre propre mise en œuvre, vous pouvez utiliser n’importe quel magasin souhaité pour sauvegarder votre mise en œuvre du gestionnaire de point de contrôle.

Créer une classe qui met en œuvre l’interface com.microsoft.azure.eventprocessorhost.ICheckpointManager

Utiliser votre mise en œuvre personnalisée du gestionnaire de point de contrôle (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Dans votre implémentation, vous pouvez remplacer le mécanisme de points de contrôle par défaut et implémenter nos propres points de contrôle en fonction de votre propre magasin de données (SQL Server, CosmosDB, Cache Azure pour Redis, etc.). Nous recommandons que le magasin utilisé pour sauvegarder votre mise en œuvre du gestionnaire de point de contrôle soit accessible à toutes les instances EPH qui traitent des événements pour le groupe de consommateurs.

Vous pouvez utiliser n’importe quel magasin de données disponible dans votre environnement.

La classe com.microsoft.azure.eventprocessorhost.EventProcessorHost vous fournit deux constructeurs qui vous permettent de remplacer le gestionnaire de point de contrôle pour votre EventProcessorHost.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé une application Java qui a reçu des messages d’un hub d’événements. Pour découvrir comment envoyer des événements à un hub d’événements à l’aide de Java, consultez [Envoyer des événements à un hub d’événements - Java](event-hubs-java-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
