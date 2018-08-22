---
title: Exemples Azure Event Hubs | Microsoft Docs
description: Exemples Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: shvija
ms.openlocfilehash: fbde6e5a5ed053d6c151b3af25535c397a496ef4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005332"
---
# <a name="event-hubs-samples"></a>Exemples de hubs d’événements 
Vous trouverez des exemples Event Hubs sur [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Ces exemples présentent les fonctionnalités clés [d’Azure Event Hubs](/azure/event-hubs/). Cette article attribue une catégorie et décrit les exemples disponibles, avec des liens vers chacun d’eux.

## <a name="net-samples"></a>Exemples relatifs à .NET

| Nom d’exemple | Description | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Cet exemple montre comment écrire une application console .NET Core qui envoie un jeu d’événements à un hub d’événements. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | Cet exemple montre comment écrire une application console .NET Core qui reçoit un jeu d’événements d’un hub d’événements à l’aide de la bibliothèque de l’hôte du processeur d’événements.  | 

## <a name="java-samples"></a>Exemples relatifs à Java

| Nom d’exemple | Description | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Cet exemple montre comment ingérer des lots d’événements dans votre hub d’événements. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Cet exemple montre comment ingérer des événements dans votre hub d’événements. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Cet exemple montre les différentes options disponibles avec Event Hubs pour ingérer des événements. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | Cet exemple montre comment recevoir des événements d’une partition de hub d’événement à l’aide d’un décalage de date et heure spécifique. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | Cet exemple montre comment recevoir des événements d’une partition de hub d’événement à l’aide d’un décalage de données spécifique. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | Cet exemple montre comment recevoir des données d’une partition de hub d’événement à l’aide d’un numéro de séquence. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Cet exemple montre comment recevoir des événements d’un hub d’événements à l’aide de l’hôte du processeur d’événements. Ce dernier fournit la sélection de partition automatique et le basculement sur plusieurs récepteurs simultanés. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | Cet exemple montre comment un hub d’événements peut automatiquement monter en puissance sur des charges élevées. L’exemple envoie des événements à un débit qui dépasse le débit configuré d’un concentrateur d’événements, provoquant la montée en puissance du hub d’événements. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Cet exemple permet de mesurer le débit d’entrée. | 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les hubs d’événements, consultez les articles suivants :

- [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
- [Fonctionnalités Event Hubs](event-hubs-features.md)
- [FAQ sur les hubs d’événements](event-hubs-faq.md)