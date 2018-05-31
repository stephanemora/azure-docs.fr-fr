---
title: Créer un espace de noms Azure Event Hubs prenant en charge Kafka | Microsoft Docs
description: Créer un espace de noms Azure Event Hubs prenant en charge Kafka avec le portail Azure
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 4f1d21be3c19dfbc764485fea47b6d4cb2171b3c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941227"
---
# <a name="create-kafka-enabled-event-hubs"></a>Créer un espace de noms Event Hubs prenant en charge Kafka

Azure Event Hubs est une plateforme PaaS (Platform as a Service) de streaming de Big Data qui ingère des millions d’événements par seconde et fournit un haut débit ainsi qu’une faible latence pour une analytique et une visualisation en temps réel.

Azure Event Hubs pour les écosystèmes Kafka vous offre un point de terminaison. Ce point de terminaison permet à votre espace de noms Event Hubs de comprendre de manière native le protocole de message et les API [Apache Kafka](https://kafka.apache.org/intro). Cette fonctionnalité vous permet de communiquer avec Event Hubs comme avec les rubriques Kafka sans changer vos clients de protocole ni exécuter vos propres clusters. Event Hubs pour les écosystèmes Kafka prend en charge [Apache Kafka versions 1.0](https://kafka.apache.org/10/documentation.html) et ultérieures.

Cet article explique comment créer un espace de noms Event Hubs et obtenir la chaîne de connexion requise pour connecter des applications Kafka à un espace de noms Event Hubs prenant en charge Kafka.

## <a name="prerequisites"></a>Prérequis


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Créer un espace de noms Event Hubs prenant en charge Kafka

1. Connectez-vous au [portail Azure][Azure portal], puis cliquez sur **Créer une ressource** en haut à gauche de l’écran.

2. Recherchez Event Hubs et sélectionnez les options indiquées ici :
    
    ![Rechercher Event Hubs dans le portail](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. **Créez un espace de noms** en fournissant un nom unique et activez Kafka sur l’espace de noms. Cliquez sur **Créer**.
    
    ![Créer un espace de noms](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Une fois l’espace de noms créé, sous l’onglet **Paramètres**, cliquez sur **Stratégies d’accès partagé** pour obtenir la chaîne de connexion.

    ![Cliquer sur Stratégies d’accès partagé](./media/event-hubs-create/create-event-hub7.png)

5. Vous pouvez choisir **RootManageSharedAccessKey** par défaut ou ajouter une nouvelle stratégie. Cliquez sur le nom de la stratégie et copiez la chaîne de connexion. 
    
    ![Sélectionner une stratégie](./media/event-hubs-create/create-event-hub8.png)
 
6. Ajoutez cette chaîne de connexion à la configuration de votre application Kafka.

Vous pouvez désormais diffuser des événements à partir de vos applications qui utilisent le protocole Kafka dans Event Hubs.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus les hubs d’événements, consultez ces liens :

* [Diffuser dans Event Hubs à partir de vos applications Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [En savoir plus sur Event Hubs pour écosystème Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
