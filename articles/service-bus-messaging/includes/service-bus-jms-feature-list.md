---
title: fichier descriptif
description: Fichier include
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/28/2021
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 6d2207b01f6ea9eff3dd2d41e9738f756f3f9fff
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220794"
---
Le tableau suivant présente les fonctionnalités Java Message Service (JMS) actuellement prises en charge par Azure Service Bus. Il présente également des fonctionnalités non prises en charge.


| Fonctionnalité | API |Statut |
|---|---|---|
| Files d’attente   | <ul> <li> JMSContext.createQueue( String queueName) </li> </ul>| **Pris en charge** |
| Rubriques   | <ul> <li> JMSContext.createTopic( String topicName) </li> </ul>| **Pris en charge** |
| Files d’attente temporaires |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Pris en charge** |
| Rubriques temporaires |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Pris en charge** |
| Producteur de messages /<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Pris en charge** |
| Explorateurs de files d'attente |<ul> <li> JMSContext.createBrowser(Queue queue) </li> <li> JMSContext.createBrowser(Queue queue, String messageSelector) </li> </ul> | **Pris en charge** |
| Consommateur de messages/ <br/> JMSConsumer | <ul> <li> JMSContext.createConsumer( Destination destination) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector, boolean noLocal)</li> </ul>  <br/> noLocal n'est actuellement pas pris en charge | **Pris en charge** |
| Abonnements durables partagés | <ul> <li> JMSContext.createSharedDurableConsumer(Topic topic, String name) </li> <li> JMSContext.createSharedDurableConsumer(Topic topic, String name, String messageSelector) </li> </ul>| **Pris en charge**|
| Abonnements durables non partagés | <ul> <li> JMSContext.createDurableConsumer(Topic topic, String name) </li> <li> createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal) </li> </ul> <br/> noLocal n'est actuellement pas pris en charge et doit être défini sur false | **Pris en charge** |
| Abonnements non durables partagés |<ul> <li> JMSContext.createSharedConsumer(Topic topic, String sharedSubscriptionName) </li> <li> JMSContext.createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector) </li> </ul> | **Pris en charge** |
| Abonnements non durables non partagés |<ul> <li> JMSContext.createConsumer(Destination destination) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector, boolean noLocal) </li> </ul> <br/> noLocal n'est actuellement pas pris en charge et doit être défini sur false | **Pris en charge** |
| Sélecteurs de messages | dépend du consommateur créé | **Pris en charge** |
| Délai de livraison (messages planifiés) | <ul> <li> JMSProducer.setDeliveryDelay( long deliveryDelay) </li> </ul>|**Pris en charge**|
| Message créé |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext.createObjectMessage( Serializable object) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext.createTextMessage( String text) </li> </ul>| **Pris en charge** |
| Transactions entre entités |<ul> <li> Connection.createSession(true, Session.SESSION_TRANSACTED) </li> </ul> | **Pris en charge** |
| Transactions distribuées || Non pris en charge |
