---
title: Guide pratique pour utiliser des files d’attente azure/service-bus dans Node.js
description: Découvrez comment écrire un programme Nodejs pour envoyer et recevoir des messages à partir d’une file d’attente Service Bus en utilisant le nouveau package @azure/service-bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: d1d0d2b57ec1ed23082101c9207c4700424dd7cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289750"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Démarrage rapide : Comment utiliser les files d’attente Service Bus avec Node.js et le package azure/service-bus
Dans ce tutoriel, vous allez apprendre à écrire un programme Nodejs pour envoyer et recevoir des messages à partir d’une file d’attente Service Bus en utilisant le nouveau package [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus). Ce package utilise le protocole [AMQP 1.0](service-bus-amqp-overview.md) plus rapide, alors que l’ancien package [azure-sb](https://www.npmjs.com/package/azure-sb) utilisait des [API d’exécution REST Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Les exemples sont écrits en JavaScript.

## <a name="prerequisites"></a>Prérequis
- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez [activer les avantages de votre abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou [vous inscrire pour un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Si vous n’avez pas de file d’attente à utiliser, suivez les étapes de l’article [Utiliser le portail Azure pour créer une file d’attente Service Bus](service-bus-quickstart-portal.md) pour créer une file d’attente. Notez la chaîne de connexion pour votre instance Service Bus et le nom de la file d’attente que vous avez créée. Nous allons utiliser ces valeurs dans les exemples.

> [!NOTE]
> - Ce didacticiel utilise des exemples que vous pouvez copier et exécuter à l’aide de [Nodejs](https://nodejs.org/). Pour obtenir des instructions sur la création d’une application Node.js, veuillez consulter les pages [Création et déploiement d’une application Node.js sur un site web Azure](../app-service/quickstart-nodejs.md) ou [Service cloud Node.js avec Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Le nouveau package [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) ne prend pas en charge la création de files d’attente pour le moment. Veuillez utiliser le package [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) si vous souhaitez les créer par programmation.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilisation de Node Package Manager (NPM) pour installer le package
Pour installer le package npm pour Service Bus, ouvrez une invite de commandes dont le chemin d’accès contient `npm`, remplacez le répertoire par le dossier où vous souhaitez stocker vos exemples, puis exécutez cette commande.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Envoi de messages à une file d'attente
L’interaction avec une file d’attente Service Bus commence par l’instanciation de la classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) et son utilisation pour instancier la classe [QueueueClient](/javascript/api/@azure/service-bus/queueclient). Une fois que vous avez le client de file d’attente, vous pouvez créer un expéditeur et lui appliquer la méthode [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) pour envoyer des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `send.js` et collez-y le code ci-dessous. Ce code envoie 10 messages vers votre file d’attente.

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
4. Ensuite, exécutez la commande `node send.js` dans une invite de commandes pour exécuter ce fichier.

Félicitations ! Vous venez d’envoyer des messages à une file d’attente Service Bus.

Les messages ont des propriétés standard comme `label` et `messageId` que vous pouvez définir lors de l’envoi. Si vous voulez définir des propriétés personnalisées, utilisez `userProperties`, qui est un objet json pouvant contenir des paires clé-valeur de vos données personnalisées.

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). Si une file d’attente n’est pas limitée par le nombre de messages qu’elle peut contenir, elle l’est en revanche par la taille totale des messages qu’elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Réception des messages d'une file d'attente
L’interaction avec une file d’attente Service Bus commence par l’instanciation de la classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) et son utilisation pour instancier la classe [QueueueClient](/javascript/api/@azure/service-bus/queueclient). Une fois que vous avez le client de la file d’attente, vous pouvez créer un récepteur et utiliser la méthode [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) ou la méthode [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) pour recevoir des messages.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com/)
2. Créez un fichier appelé `recieve.js` et collez-y le code ci-dessous. Ce code tentera de recevoir 10 messages de votre file d’attente. Le nombre réel de messages que vous recevrez dépend du nombre de messages dans la file d’attente et de la latence du réseau.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
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
4. Ensuite, exécutez la commande `node receiveMessages.js` dans une invite de commandes pour exécuter ce fichier.

Félicitations ! Vous venez de recevoir des messages d’une file d’attente Service Bus.

La méthode [createReceiver](/javascript/api/@azure/service-bus/queueclient#createreceiver-receivemode-) accepte un `ReceiveMode` qui correspond à une énumération avec les valeurs [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) et [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). N’oubliez pas de [régler vos messages](message-transfers-locks-settlement.md#settling-receive-operations) si vous utilisez le mode `PeekLock` en appliquant l’une des méthodes `complete()`, `abandon()`, `defer()` ou `deadletter()` au message.

> [!NOTE]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer permet aux utilisateurs de se connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction d’importation/exportation ou la possibilité de tester une rubrique, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus, consultez les ressources suivantes :
- [Files d’attente, rubriques et abonnements.](service-bus-queues-topics-subscriptions.md)
- Consulter d’autres exemples [Nodejs pour Service Bus sur GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centre de développement Node.js](https://azure.microsoft.com/develop/nodejs/)