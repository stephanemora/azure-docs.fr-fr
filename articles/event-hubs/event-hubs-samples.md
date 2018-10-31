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
ms.openlocfilehash: ed337689360428d66657e5391ee52bb0ae39dc14
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365643"
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

## <a name="python-samples"></a>Exemples Python
Vous trouverez des exemples Python pour Azure Event Hubs dans le dépôt GitHub [azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples).

## <a name="nodejs-samples"></a>Exemples relatifs à Node.js
Vous trouverez des exemples Node.js pour Azure Event Hubs dans le dépôt GitHub [azure-event-hubs-node](https://github.com/Azure/azure-event-hubs-node).

## <a name="go-samples"></a>Exemples Go
Vous trouverez des exemples Go pour Azure Event Hubs dans le dépôt GitHub [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="azure-cli-samples"></a>Exemples d’interface de ligne de commande Azure
Vous trouverez des exemples d’interface de ligne de commande Azure pour Azure Event Hubs dans le dépôt GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI).

## <a name="azure-powershell-samples"></a>Exemples Azure PowerShell
Vous trouverez des exemples Azure PowerShell pour Azure Event Hubs dans le dépôt GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell).
 


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les hubs d’événements, consultez les articles suivants :

- [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
- [Fonctionnalités Event Hubs](event-hubs-features.md)
- [FAQ sur les hubs d’événements](event-hubs-faq.md)