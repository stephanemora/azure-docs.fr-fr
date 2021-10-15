---
title: Fichier include
description: Fichier include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c6c6a92848b91f32dec612abe7c16ab73b71cccd
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300425"
---
## <a name="trusted-microsoft-services"></a>Services Microsoft approuvés
Lorsque vous activez le paramètre **Autoriser les services Microsoft approuvés pour contourner ce pare-feu**, les services suivants sont autorisés à accéder à vos ressources Service Bus.

| Service approuvé | Scénarios d’utilisation pris en charge | 
| --------------- | ------------------------- | 
| Azure Event Grid | Autorise Azure Event Grid à envoyer des événements vers les files d’attente ou les rubriques dans votre espace de noms Service Bus. Vous devez également procéder comme suit : <ul><li>Activer une identité attribuée par le système pour un sujet ou un domaine</li><li>Ajouter l’identité au rôle d’expéditeur de données Azure Service Bus sur l’espace de noms Service Bus</li><li>Ensuite, configurez l’abonnement aux événements qui utilise une file d’attente ou une rubrique Service Bus comme point de terminaison pour utiliser l’identité affectée au système.</li></ul> <p>Pour plus d’informations, consultez [Remise d’événement avec une identité managée](../../event-grid/managed-service-identity.md)</p>|
| Gestion des API Azure | <p>Le service API Management vous permet d'envoyer des messages à une file d'attente/rubrique Service Bus de votre espace de noms Service Bus.</p><ul><li>Vous pouvez déclencher des flux de travail personnalisés en envoyant des messages à votre file d'attente/rubrique Service Bus lorsqu'une API est appelée à l'aide de la [stratégie send-request](../../api-management/api-management-sample-send-request.md).</li><li>Vous pouvez également traiter une file d'attente/rubrique Service Bus comme back-end dans une API. Pour obtenir un exemple de stratégie, consultez [S'authentifier à l'aide d'une identité managée pour accéder à une file d'attente ou à une rubrique Service Bus](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). Vous devez également procéder comme suit :<ol><li>Activez l'identité attribuée par le système sur l'instance d'API Management. Pour obtenir des instructions, consultez [Utiliser des identités managées dans Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md).</li><li>Ajouter l’identité au rôle **Expéditeur de données Azure Service Bus** dans l’espace de noms Service Bus</li></ol></li></ul> | 
| Azure IoT Central | <p>Autorise IoT Central à exporter des données vers des files d’attente ou rubriques Service Bus dans votre espace de noms Service Bus. Vous devez également procéder comme suit :</p><ul><li>Activez l’identité affectée par le système pour votre application IoT Central.</li><li>Ajoutez l’identité au rôle **Expéditeur de données Azure Service Bus** dans l’espace de noms Service Bus. </li><li>Ensuite, configurez la [destination d’exportation Service Bus sur votre application IoT Central](../../iot-central/core/howto-export-data.md) pour utiliser l’authentification basée sur l’identité. </li>
