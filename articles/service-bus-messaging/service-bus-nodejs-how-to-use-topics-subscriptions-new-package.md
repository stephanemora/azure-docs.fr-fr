---
title: Utiliser les rubriques et les abonnements azure/service-bus avec Node.js
description: 'Démarrage rapide : Découvrez comment utiliser les rubriques et abonnements Service Bus dans Azure à partir d’une application Node.js.'
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
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 160f9831a23ed16fc33ddbbb9b4e07a5627a3f9e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462135"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Démarrage rapide : Utilisation des rubriques et abonnements Service Bus avec Node.js et le package azure/service-bus
> [!div class="op_multi_selector" title1="Langage de programmation" title2="Package Node.js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Ce didacticiel explique comment écrire un programme Node.js pour envoyer des messages à une rubrique Service Bus et recevoir des messages d’un abonnement Service Bus à l’aide du nouveau package [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus). Ce package utilise le protocole [AMQP 1.0](service-bus-amqp-overview.md) plus rapide, alors que l’ancien package [azure-sb](https://www.npmjs.com/package/azure-sb) utilisait des [API d’exécution REST Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Les exemples sont écrits en JavaScript.

## <a name="prerequisites"></a>Conditions préalables requises
- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez [activer les avantages de votre abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou [vous inscrire pour un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Si vous n’avez pas de rubrique ou d’abonnement à utiliser, suivez les étapes décrites dans l’article [Utiliser le portail Azure pour créer des rubriques et des abonnements Service Bus](service-bus-quickstart-topics-subscriptions-portal.md) afin de les créer. Notez la chaîne de connexion pour votre instance Service Bus et le nom de la rubrique et de l’abonnement que vous avez créés. Nous allons utiliser ces valeurs dans les exemples.

> [!NOTE]
> - Ce didacticiel utilise des exemples que vous pouvez copier et exécuter à l’aide de [Nodejs](https://nodejs.org/). Pour obtenir des instructions sur la création d’une application Node.js, veuillez consulter les pages [Création et déploiement d’une application Node.js sur un site web Azure](../app-service/app-service-web-get-started-nodejs.md) ou [Service cloud Node.js avec Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Le nouveau package [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) ne prend pas encore en charge la création de rubriques et d’abonnements. Veuillez utiliser le package [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) si vous souhaitez les créer par programmation.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilisation de Node Package Manager (NPM) pour installer le package
Pour installer le package npm pour Service Bus, ouvrez une invite de commandes dont le chemin d’accès contient `npm`, remplacez le répertoire par le dossier où vous souhaitez stocker vos exemples, puis exécutez cette commande.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique
L’interaction avec une rubrique Service Bus commence par l’instanciation de la classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) et son utilisation pour instancier la classe [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient). Une fois que vous avez le client de rubrique, vous pouvez créer un expéditeur et lui appliquer la méthode [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) pour envoyer des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `send.js` et collez-y le code ci-dessous. Ce code envoie 10 messages vers votre rubrique.

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
3. Entrez la chaîne de connexion et le nom de rubrique dans le code ci-dessus.
4. Ensuite, exécutez la commande `node send.js` dans une invite de commandes pour exécuter ce fichier. 

Félicitations ! Vous venez d’envoyer des messages à une file d’attente Service Bus.

Les messages ont des propriétés standard comme `label` et `messageId` que vous pouvez définir lors de l’envoi. Si vous voulez définir des propriétés personnalisées, utilisez `userProperties`, qui est un objet json pouvant contenir des paires clé-valeur de vos données personnalisées.

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). Si une rubrique n’est pas limitée par le nombre de messages qu’elle peut contenir, elle l’est en revanche par la taille totale des messages qu’elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement
L’interaction avec un abonnement Service Bus commence par l’instanciation de la classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) et son utilisation pour instancier la classe [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient). Une fois que vous avez le client d’abonnement, vous pouvez créer un récepteur et utiliser la méthode [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou la méthode [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) pour recevoir des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `recieve.js` et collez-y le code ci-dessous. Ce code tentera de recevoir 10 messages de votre abonnement. Le nombre réel de messages que vous recevrez dépend du nombre de messages dans l’abonnement et de la latence du réseau.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
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
3. Entrez la chaîne de connexion et les noms de votre rubrique et de votre abonnement dans le code ci-dessus.
4. Ensuite, exécutez la commande `node receiveMessages.js` dans une invite de commandes pour exécuter ce fichier.

Félicitations ! Vous venez de recevoir des messages d’un abonnement Service Bus.

La méthode [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) accepte un `ReceiveMode` qui correspond à une énumération avec les valeurs [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) et [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). N’oubliez pas de [régler vos messages](message-transfers-locks-settlement.md#settling-receive-operations) si vous utilisez le mode `PeekLock` en appliquant l’une des méthodes `complete()`, `abandon()`, `defer()` ou `deadletter()` au message.

## <a name="subscription-filters-and-actions"></a>Filtres et actions d’abonnement
Service Bus prend en charge les [filtres et actions sur les abonnements](topic-filters.md). Cela vous permet de filtrer les messages entrants d’un abonnement et de modifier leurs propriétés.

Une fois que vous avez une instance de `SubscriptionClient`, vous pouvez utiliser les méthodes ci-dessous pour obtenir, ajouter et supprimer des règles sur l’abonnement afin de contrôler les filtres et les actions.

- getRules
- addRule
- removeRule

Chaque abonnement dispose d’une règle par défaut qui utilise le filtre true pour autoriser tous les messages entrants. Lorsque vous ajoutez une nouvelle règle, n’oubliez pas de supprimer le filtre par défaut pour que le filtre dans votre nouvelle règle puisse fonctionner. Si un abonnement n’a aucune règle, il ne reçoit aucun message.

> [!NOTE]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer permet aux utilisateurs de se connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction d’importation/exportation ou la possibilité de tester une rubrique, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus, consultez les ressources suivantes :

- [Files d’attente, rubriques et abonnements.](service-bus-queues-topics-subscriptions.md)
- Consulter d’autres exemples [Nodejs pour Service Bus sur GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centre de développement Node.js](https://azure.microsoft.com/develop/nodejs/)


