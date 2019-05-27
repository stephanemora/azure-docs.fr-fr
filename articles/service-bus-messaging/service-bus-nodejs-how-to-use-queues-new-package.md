---
title: Comment utiliser les files d’attente Azure Service Bus dans Node.js - /-bus des services azure | Microsoft Docs
description: Découvrez comment utiliser les files d’attente Service Bus dans Azure à partir d’une application Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988351"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Comment utiliser les files d’attente Service Bus avec Node.js et le package azure/service bus
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Dans ce didacticiel, vous allez apprendre à écrire un programme Nodejs pour envoyer des messages à et recevoir des messages à partir d’une file d’attente Service Bus à l’aide de la nouvelle [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) package. Ce package utilise le plus rapide [protocole AMQP 1.0](service-bus-amqp-overview.md) tandis que l’ancien [azure-sb](https://www.npmjs.com/package/azure-sb) package utilisé [API d’exécution REST Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Les exemples sont écrits en JavaScript.

## <a name="prerequisites"></a>Conditions préalables
- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez activer votre [avantages pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Si vous n’avez pas une file d’attente pour travailler avec, suivez les étapes de la [utiliser le portail Azure pour créer une file d’attente Service Bus](service-bus-quickstart-portal.md) article pour créer une file d’attente. Notez la chaîne de connexion pour votre instance de Service Bus et le nom de la file d’attente que vous avez créé. Nous allons utiliser ces valeurs dans les exemples.

> [!NOTE]
> - Ce didacticiel fonctionne avec les exemples que vous pouvez copier et exécuter à l’aide de [Nodejs](https://nodejs.org/). Pour obtenir des instructions sur la façon de créer une application Node.js, consultez [créer et déployer une application Node.js sur un site Web Azure](../app-service/app-service-web-get-started-nodejs.md), ou [service de cloud de Node.js à l’aide de Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - La nouvelle [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) package ne prend pas en charge la création de files d’attente encore. Utilisez le [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) package si vous souhaitez les créer par programmation.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilisation de Node Package Manager (NPM) pour installer le package
Pour installer le package npm pour Service Bus, ouvrez une invite de commandes a `npm` dans son chemin d’accès, accédez au répertoire dans le dossier où vous souhaitez avoir vos exemples, puis exécutez cette commande.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Envoi de messages à une file d'attente
Interaction avec un Bus de Service de file d’attente démarre avec l’instanciation du [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe et l’utiliser pour instancier le [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) classe. Une fois que vous avez le client de file d’attente, vous pouvez créer un expéditeur et utilisez [envoyer](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) méthode dessus pour envoyer des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `send.js` et collez le code il ci-dessous. Ce code envoie 10 messages à votre file d’attente.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Entrez la chaîne de connexion et le nom de votre file d’attente dans le code ci-dessus.
4. Puis exécutez la commande `node send.js` dans une invite de commandes pour exécuter ce fichier.

Félicitations ! Vous venez d’envoyer les messages à une file d’attente Service Bus.

Messages ont des propriétés standards telles que `label` et `messageId` que vous pouvez définir lors de l’envoi. Si vous souhaitez définir toutes les propriétés personnalisées, utilisez le `userProperties`, qui est un objet json qui peut contenir des paires clé-valeur de vos données personnalisées.

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). Il n’existe aucune limite sur le nombre de messages contenus dans une file d’attente, mais il existe une limite sur la taille totale des messages détenues par une file d’attente. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Réception des messages d'une file d'attente
Interaction avec un Bus de Service de file d’attente démarre avec l’instanciation du [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe et l’utiliser pour instancier le [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) classe. Une fois que vous avez le client de file d’attente, vous pouvez créer un récepteur et utilisez [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) méthode dessus pour recevoir des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `recieve.js` et collez le code il ci-dessous. Ce code tentera de recevoir des messages de 10 à partir de votre file d’attente. Le nombre réel que vous recevez varie selon le nombre de messages dans la latence réseau et de la file d’attente.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Entrez la chaîne de connexion et le nom de votre file d’attente dans le code ci-dessus.
4. Puis exécutez la commande `node receiveMessages.js` dans une invite de commandes pour exécuter ce fichier.

Félicitations ! Vous venez de recevoir les messages à partir d’une file d’attente Service Bus.

Le [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) méthode prend un `ReceiveMode` qui est une énumération avec les valeurs [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) et [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). N’oubliez pas de [régler vos messages](message-transfers-locks-settlement.md#settling-receive-operations) si vous utilisez le `PeekLock` mode à l’aide des `complete()`, `abandon()`, `defer()`, ou `deadletter()` méthodes sur le message.

> [!NOTE]
> Vous pouvez gérer les ressources de Service Bus avec [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). L’Explorateur Service Bus permet aux utilisateurs de se connecter à un espace de noms Service Bus et administrer les entités de messagerie de manière simple. L’outil fournit des fonctionnalités avancées telles que la fonctionnalité d’importation/exportation ou de la possibilité de tester une rubrique, files d’attente, abonnements, services de relais, hubs de notification et hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes.
- [Files d’attente, rubriques et abonnements.](service-bus-queues-topics-subscriptions.md)
- Consulter d’autres [exemples de Nodejs pour Service Bus sur GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centre de développement Node.js](https://azure.microsoft.com/develop/nodejs/)

