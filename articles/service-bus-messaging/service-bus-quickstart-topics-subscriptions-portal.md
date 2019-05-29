---
title: Démarrage rapide - Utiliser le portail Azure pour créer des rubriques et des abonnements Service Bus | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer une rubrique Service Bus et des abonnements à cette rubrique à l’aide du portail Azure.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: a392f8b11a7ab1ad72f4da289c54e34b022f1ea6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990309"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Démarrage rapide : utiliser le portail Azure pour créer une rubrique Service Bus et des abonnements à cette rubrique
Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer une rubrique Service Bus, puis des abonnements à cette rubrique. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Présentation des rubriques et des abonnements Service Bus
Les rubriques et les abonnements Service Bus prennent en charge un modèle de communication de messagerie *de publication et d'abonnement* . Lors de l’utilisation de rubriques et d’abonnements, les composants d’une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une rubrique, qui fait office d’intermédiaire.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrairement aux files d’attente Service Bus, où chaque message est traité par un seul consommateur, les rubriques et les abonnements fournissent une forme de communication « un-à-plusieurs », à l’aide d’un modèle de publication et d’abonnement. Il est possible d’inscrire plusieurs abonnements à une rubrique. Lorsqu’un message est envoyé à une rubrique, il est alors mis à disposition de chaque abonnement pour être géré ou traité indépendamment. Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Vous pouvez éventuellement inscrire des règles de filtre pour une rubrique par abonnement, ce qui vous permet de filtrer ou de restreindre les messages d’une rubrique reçus en fonction des abonnements à une rubrique.

Les rubriques et les abonnements Service Bus vous permettent de mettre votre infrastructure à l’échelle pour traiter de nombreux messages parmi un grand nombre d’utilisateurs et d’applications.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer permet aux utilisateurs de se connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L'outil fournit des fonctionnalités avancées telles que l'importation/l’exportation ou la possibilité de tester des sujets, des files d'attente, des abonnements, des services de relais, des hubs de notification et des hubs d'événements. 

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment envoyer des messages à une rubrique et recevoir ces messages par le biais d’un abonnement, consultez l’article correspondant au langage de programmation de votre choix dans la table des matières. 

> [!div class="nextstepaction"]
> [Publier et s’abonner à des messages](service-bus-dotnet-how-to-use-topics-subscriptions.md)
