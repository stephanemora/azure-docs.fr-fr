---
title: Envoyer des événements vers Azure Event Hubs avec Java | Microsoft Docs
description: Prise en main de l’envoi vers Event Hubs avec Java
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 08/27/2018
ms.author: shvija
ms.openlocfilehash: f67982eda60a8fdfdf0d50785827c513275fd202
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124753"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Envoyer des événements vers Azure Event Hubs avec Java

Les concentrateurs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectées dans un concentrateur d’événements, les données peuvent être transformées et stockées à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la section [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce didacticiel explique comment envoyer des événements vers un concentrateur d’événements à l’aide d’une application console en Java. Pour recevoir des événements avec le processeur d’événements Java, consultez [cet article](event-hubs-java-get-started-receive-eph.md) ou cliquez sur le langage de réception approprié dans le sommaire à gauche.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un environnement de développement Java. Ce tutoriel utilise [Fiddler](https://www.eclipse.org/).
* Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.

Le code de ce tutoriel est basé sur [l’exemple GitHub SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), que vous pouvez examiner pour voir la version complète de l’application.

## <a name="send-events-to-event-hubs"></a>Envoyer des événements vers Event Hubs

La bibliothèque cliente de Java pour les concentrateurs d’événements peut être utilisée dans les projets Maven à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Vous pouvez référencer cette bibliothèque à l’aide de la déclaration de dépendance suivante au sein de votre fichier projet Maven. La version actuelle est 1.0.2 :    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Pour différents types d’environnements de génération, vous pouvez obtenir explicitement les fichiers JAR les plus récents à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Pour un éditeur d’événements simple, importez le package *com.microsoft.azure.eventhubs* pour les classes clientes Event Hubs et le package *com.microsoft.azure.servicebus* pour les classes utilitaires, telles que les exceptions courantes qui sont partagées avec le client de messagerie Azure Service Bus. 

### <a name="declare-the-send-class"></a>Déclarer la classe Envoyer

Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. Nommez la classe `SimpleSend` :     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Construire la chaîne de connexion

Utilisez la classe ConnectionStringBuilder pour construire une valeur de chaîne de connexion et passer à l’instance client Event Hubs. Remplacez les espaces réservés par les valeurs obtenues lors de la création de l’espace de noms et du concentrateur d’événements :

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>Envoyer des événements

Créez un événement unique en transformant une chaîne dans son encodage UTF-8 octets. Ensuite, créez une instance cliente Event Hubs à partir de la chaîne de connexion et envoyez le message :   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

### <a name="how-messages-are-routed-to-eventhub-partitions"></a>Comment les messages sont acheminés vers les partitions EventHub

Avant que les messages ne soient récupérés par les consommateurs, ils doivent d’abord être publiés sur les partitions par les éditeurs. Lorsque les messages sont publiés sur Event Hub de façon synchrone à l’aide de la méthode sendSync() sur l’objet com.microsoft.azure.eventhubs.EventHubClient, le message peut être envoyé à une partition spécifique ou distribué à toutes les partitions disponibles par tourniquet (round robin) selon que la clé de partition est spécifiée ou non.

Lorsqu’une chaîne représentant la clé de partition est spécifiée, la clé sera hachée pour déterminer à quelle partition envoyer l’événement.

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

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Recevoir des événements avec EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Vue d’ensemble des hubs d’événements][Event Hubs overview]
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

