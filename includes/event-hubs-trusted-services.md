---
title: Fichier include
description: Fichier include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 743d7d46474b4ba55df50398f29cbdb964b5ff08
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978768"
---
## <a name="trusted-microsoft-services"></a>Services Microsoft approuvés
Lorsque vous activez le paramètre **Autoriser les services Microsoft approuvés pour contourner ce pare-feu**, les services suivants sont autorisés à accéder à vos ressources Event Hubs.

| Service approuvé | Scénarios d’utilisation pris en charge | 
| --------------- | ------------------------- | 
| Azure Event Grid | Autorise Azure Event Grid à envoyer des événements à Event Hubs dans votre espace de noms Event Hubs. Vous devez également procéder comme suit : <ul><li>Activer une identité attribuée par le système pour un sujet ou un domaine</li><li>Ajouter l’identité au rôle d’expéditeur de données Azure Event Hubs sur l’espace de noms Event Hubs</li><li>Ensuite, configurez l’abonnement aux événements qui utilise un hub d’événements comme point de terminaison pour utiliser l’identité affectée au système.</li></ul> <p>Pour plus d’informations, consultez [Remise d’événement avec une identité managée](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (paramètres de diagnostic) | Autorise Azure Monitor à envoyer des informations de diagnostic à Event Hubs dans votre espace de noms Event Hubs. Azure Monitor peut lire à partir de l’Event Hub et y écrire des données. |
| Azure Stream Analytics | Permet à un travail Azure Stream Analytics de lire des données ([entrée](../articles/stream-analytics/stream-analytics-add-inputs.md)) ou d’écrire des données dans des hubs d’événements ([sortie](../articles/stream-analytics/event-hubs-output.md)) dans votre espace de noms Event Hubs. <p>**Important !** Le travail Stream Analytics doit être configuré de manière à utiliser une **identité managée** pour accéder à l’Event Hub. Pour plus d’informations, consultez [Accès à Event Hubs à partir d’un travail Azure Stream Analytics à l’aide des identités managées (préversion)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Autorise IoT Hub à envoyer des messages aux hubs d’événements dans votre espace de noms Event Hub. Vous devez également procéder comme suit : <ul><li>Activer une identité attribuée par le système pour le hub IoT.</li><li>Ajouter l’identité au rôle d’expéditeur de données Azure Event Hubs sur l’espace de noms Event Hubs.</li><li>Ensuite, configurer l’IoT Hub qui utilise un Event Hub comme point de terminaison personnalisé pour utiliser l’authentification basée sur l’identité.</li></ul>
