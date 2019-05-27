---
title: Utilisation des rubriques et abonnements Azure Service Bus avec Node.js | Microsoft Docs
description: Découvrez comment utiliser les rubriques et abonnements Service Bus dans Azure à partir d’une application Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992134"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Utilisation des rubriques et abonnements Service Bus avec Node.js et le package azure/service bus
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Dans ce didacticiel, vous allez apprendre à écrire un programme Node.js pour envoyer des messages à une rubrique Service Bus et de recevoir des messages à partir d’un abonnement Service Bus à l’aide de la nouvelle [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) package. Ce package utilise le plus rapide [protocole AMQP 1.0](service-bus-amqp-overview.md) tandis que l’ancien [azure-sb](https://www.npmjs.com/package/azure-sb) package utilisé [API d’exécution REST Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Les exemples sont écrits en JavaScript.

## <a name="prerequisites"></a>Conditions préalables
- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez activer votre [avantages pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Si vous n’avez pas une rubrique et un abonnement pour travailler avec, suivez les étapes de la [utiliser le portail Azure pour créer un Service Bus rubriques et abonnements](service-bus-quickstart-topics-subscriptions-portal.md) article pour les créer. Notez la chaîne de connexion pour votre instance de Service Bus et les noms de la rubrique et abonnement que vous avez créé. Nous allons utiliser ces valeurs dans les exemples.

> [!NOTE]
> - Ce didacticiel fonctionne avec les exemples que vous pouvez copier et exécuter à l’aide de [Nodejs](https://nodejs.org/). Pour obtenir des instructions sur la façon de créer une application Node.js, consultez [créer et déployer une application Node.js sur un site Web Azure](../app-service/app-service-web-get-started-nodejs.md), ou [Node.js Cloud Service à l’aide de Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - La nouvelle [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) package ne prend pas en charge la création d’abonnements et topcis encore. Utilisez le [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) package si vous souhaitez les créer par programmation.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilisation de Node Package Manager (NPM) pour installer le package
Pour installer le package npm pour Service Bus, ouvrez une invite de commandes a `npm` dans son chemin d’accès, accédez au répertoire dans le dossier où vous souhaitez avoir vos exemples, puis exécutez cette commande.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique
Interaction avec un Service Bus rubrique commence avec l’instanciation du [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe et l’utiliser pour instancier le [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) classe. Une fois que vous avez le client de rubrique, vous pouvez créer un expéditeur et utilisez [envoyer](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) méthode dessus pour envoyer des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `send.js` et collez le code il ci-dessous. Ce code envoie 10 messages à votre rubrique.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Entrez la chaîne de connexion et le nom de votre rubrique dans le code ci-dessus.
4. Puis exécutez la commande `node send.js` dans une invite de commandes pour exécuter ce fichier. 

Félicitations ! Vous venez d’envoyer les messages à une file d’attente Service Bus.

Messages ont des propriétés standards telles que `label` et `messageId` que vous pouvez définir lors de l’envoi. Si vous souhaitez définir toutes les propriétés personnalisées, utilisez le `userProperties`, qui est un objet json qui peut contenir des paires clé-valeur de vos données personnalisées.

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). Il n’existe aucune limite sur le nombre de messages contenus dans une rubrique, mais il existe une limite sur la taille totale des messages détenues par une rubrique. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement
Interaction avec un Bus de Service d’abonnement démarre avec l’instanciation du [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe et l’utiliser pour instancier le [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) classe. Une fois que vous avez le client d’abonnement, vous pouvez créer un récepteur et utilisez [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) méthode dessus pour recevoir des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `recieve.js` et collez le code il ci-dessous. Ce code tentera de recevoir des messages de 10 à partir de votre abonnement. Le nombre réel que vous recevez varie selon le nombre de messages dans l’abonnement et la latence du réseau.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Dans le code ci-dessus, entrez la chaîne de connexion et les noms de rubrique et votre abonnement.
4. Puis exécutez la commande `node receiveMessages.js` dans une invite de commandes pour exécuter ce fichier.

Félicitations ! Vous venez de recevoir les messages à partir d’un abonnement Service Bus.

Le [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) méthode prend un `ReceiveMode` qui est une énumération avec les valeurs [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) et [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). N’oubliez pas de [régler vos messages](message-transfers-locks-settlement.md#settling-receive-operations) si vous utilisez le `PeekLock` mode à l’aide des `complete()`, `abandon()`, `defer()`, ou `deadletter()` méthodes sur le message.

## <a name="subscription-filters-and-actions"></a>Actions et les filtres d’abonnement
Service Bus prend en charge [filtres et des actions sur les abonnements](topic-filters.md), qui vous permet de filtrer les messages entrants à un abonnement et pour modifier leurs propriétés.

Une fois que vous avez une instance d’un `SubscriptionClient` vous pouvez utiliser le ci-dessous méthodes dessus pour obtenir, ajouter et supprimer des règles sur l’abonnement pour contrôler les filtres et les actions.

- getRules
- addRule
- removeRule

Chaque abonnement dispose d’une règle par défaut qui utilise le filtre true pour autoriser tous les messages entrants. Lorsque vous ajoutez une nouvelle règle, n’oubliez pas de supprimer le filtre par défaut pour le filtre dans votre nouvelle règle puisse fonctionner. Si un abonnement n’a aucune règle, il ne recevra aucun message.

> [!NOTE]
> Vous pouvez gérer les ressources de Service Bus avec [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). L’Explorateur Service Bus permet aux utilisateurs de se connecter à un espace de noms Service Bus et administrer les entités de messagerie de manière simple. L’outil fournit des fonctionnalités avancées telles que la fonctionnalité d’importation/exportation ou de la possibilité de tester une rubrique, files d’attente, abonnements, services de relais, hubs de notification et hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes.

- [Files d’attente, rubriques et abonnements.](service-bus-queues-topics-subscriptions.md)
- Consulter d’autres [exemples de Nodejs pour Service Bus sur GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centre de développement Node.js](https://azure.microsoft.com/develop/nodejs/)


