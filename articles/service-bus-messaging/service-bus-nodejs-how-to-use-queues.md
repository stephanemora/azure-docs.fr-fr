---
title: Comment utiliser les files d’attente Azure Service Bus dans Node.js | Microsoft Docs
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
ms.openlocfilehash: 1426b3d31159280ad9aac2dd240a5f083c40752d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988304"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azure-sb-package"></a>Comment utiliser les files d’attente Service Bus avec Node.js et le package azure-sb
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Dans ce didacticiel, vous allez apprendre à créer des applications Node.js à envoyer des messages à et de recevoir des messages à partir d’une file d’attente Service Bus à l’aide du [azure-sb](https://www.npmjs.com/package/azure-sb) package. Les exemples sont écrits en JavaScript et utiliser le Node.js [module Azure](https://www.npmjs.com/package/azure) qui utilise en interne le `azure-sb` package.

Le [azure-sb](https://www.npmjs.com/package/azure-sb) package utilise [API d’exécution REST Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Vous pouvez obtenir une expérience plus rapide à l’aide de la nouvelle [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) package qui utilise le plus rapide [protocole AMQP 1.0](service-bus-amqp-overview.md). Pour en savoir plus sur le nouveau package, consultez [comment utiliser les files d’attente Service Bus avec Node.js et @azure/service-bus package](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), sinon poursuivez votre lecture pour voir comment utiliser le [azure](https://www.npmjs.com/package/azure) package.

## <a name="prerequisites"></a>Conditions préalables
- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez activer votre [avantages pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Si vous n’avez pas une file d’attente pour travailler avec, suivez les étapes de la [utiliser le portail Azure pour créer une file d’attente Service Bus](service-bus-quickstart-portal.md) article pour créer une file d’attente.
    1. Lire le plus rapide pour **vue d’ensemble** de Service Bus **files d’attente**. 
    2. Créer un Service Bus **espace de noms**. 
    3. Obtenir le **chaîne de connexion**. 

        > [!NOTE]
        > Vous allez créer un **file d’attente** dans l’espace de noms Service Bus à l’aide de Node.js dans ce didacticiel. 
 

## <a name="create-a-nodejs-application"></a>Création d’une application Node.js
Créez une application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, voir les pages [Création et déploiement d’une application Node.js sur un site web Azure][Create and deploy a Node.js application to an Azure Website] ou [Service cloud Node.js avec Windows PowerShell][Node.js Cloud Service].

## <a name="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l’utilisation de Service Bus
Pour utiliser Azure Service Bus, téléchargez et utilisez le package Azure Node.js. Ce dernier inclut des bibliothèques permettant de communiquer avec les services REST de Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilisation de Node Package Manager (NPM) pour obtenir le package
1. Utilisez la fenêtre de commande **Windows PowerShell for Node.js** pour accéder au dossier **c:\\node\\sbqueues\\WebRole1** dans lequel vous avez créé votre exemple d’application.
2. Type **npm installer azure** dans la fenêtre de commande, ce qui génère une sortie similaire à l’exemple suivant :

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node_modules** a été créé. Dans ce dossier, recherchez le **azure** package qui contient les bibliothèques que vous avez besoin pour accéder aux files d’attente Service Bus.

### <a name="import-the-module"></a>Importation du module
À l’aide d’un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l’application :

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configuration d’une connexion Service Bus Azure
Le module Azure lit la variable d’environnement `AZURE_SERVICEBUS_CONNECTION_STRING` pour obtenir les informations nécessaires pour se connecter à Service Bus. Si cette variable d’environnement n’est pas définie, vous devez spécifier les informations de compte lors de l’appel `createServiceBusService`.

Pour un exemple de configuration des variables d’environnement dans le [portail Azure][Azure portal] pour un site web Azure, consultez [Application web Node.js avec stockage][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Créer une file d'attente
L’objet **ServiceBusService** permet d’utiliser des files d’attente Service Bus. Le code suivant crée un objet **ServiceBusService**. Ajoutez-le vers le début du fichier **server.js**, après l’instruction relative à l’importation du module Azure :

```javascript
var serviceBusService = azure.createServiceBusService();
```

En appelant `createQueueIfNotExists` sur l’objet **ServiceBusService**, la file d’attente spécifiée est renvoyée (si elle existe) ou une file d’attente comportant le nom spécifié est créée. Le code suivant utilise `createQueueIfNotExists` pour créer la file d’attente nommée `myqueue` ou s’y connecter :

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

La méthode `createServiceBusService` prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de file d’attente par défaut comme la durée de vie du message ou la taille maximale de la file d’attente. L’exemple suivant définit la taille maximale de la file d’attente sur 5 Go et la durée de vie de message sur 1 minute :

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtres
Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées par le biais de **ServiceBusService**. Il peut s’agir d’opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

```javascript
function handle (requestOptions, next)
```

Après le prétraitement des options de la requête, la méthode doit appeler `next` en passant un rappel avec la signature suivante :

```javascript
function (returnObject, finalCallback, next)
```

Dans ce rappel et après le traitement de la `returnObject` (la réponse de la demande au serveur), le rappel doit appeler `next` si elle existe, pour continuer à traiter d’autres filtres ou appeler `finalCallback`, ce qui met fin à l’appel du service .

Deux filtres, `ExponentialRetryPolicyFilter` et `LinearRetryPolicyFilter`, implémentant une logique de nouvelle tentative sont inclus avec le kit de développement logiciel (SDK) Azure pour Node.js. Le code suivant permet de créer un objet `ServiceBusService` utilisant `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Envoi de messages à une file d'attente
Pour envoyer un message à une file d’attente Service Bus, votre application appelle la méthode `sendQueueMessage` sur l’objet **ServiceBusService**. Les messages envoyés aux files d’attente Service Bus (et reçus de celles-ci) sont les objets **BrokeredMessage**. Ils possèdent un ensemble de propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu’un corps de données d’application arbitraires. Une application peut définir le corps du message en passant une chaîne comme message. Toutes les propriétés standard requises sont remplies avec les valeurs par défaut.

L’exemple suivant indique comment envoyer un message test à la file d’attente nommée `myqueue` au moyen de la méthode `sendQueueMessage` :

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite sur le nombre de messages contenus dans une file d’attente, mais il existe une limite sur la taille totale des messages détenues par une file d’attente. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Réception des messages d'une file d'attente
La méthode `receiveQueueMessage` de l’objet **ServiceBusService** permet de recevoir les messages d’une file d’attente. Par défaut, les messages sont supprimés de la file d’attente au fur et à mesure de leur lecture. Cependant, vous pouvez lire et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre facultatif `isPeekLock` sur **true**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l’opération de réception est le modèle le plus simple et mieux adapté aux scénarios dans lesquels une application peut tolérer de ne pas traiter un message lorsqu’une défaillance se produit. Pour mieux comprendre ce comportement, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Étant donné que Service Bus a marqué le message comme étant consommé, puis lorsque l’application redémarre et recommence à consommer des messages, elle aura manqué le message qui a été consommé avant l’incident.

Si le `isPeekLock` paramètre est défini sur **true**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peut pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode `deleteMessage` et en fournissant le message à supprimer sous la forme d’un paramètre. La méthode `deleteMessage` marque le message comme étant utilisé et le supprime de la file d’attente.

L’exemple suivant montre comment recevoir et traiter des messages à l’aide de la méthode `receiveQueueMessage`. L’exemple reçoit et supprime un message, reçoit un message en utilisant la méthode `isPeekLock` définie sur **true**, puis supprime le message à l’aide de la méthode `deleteMessage` :

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d’application et des messages illisibles
Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode `unlockMessage` pour l’objet **ServiceBusService**. Cela entraînera le Service Bus à déverrouiller le message dans la file d’attente et le rendre disponible pour être à nouveau, reçu par la même application consommatrice ou par une autre application consommatrice.

Il existe également un délai d’expiration associé à un message verrouillé dans la file d’attente et si l’application ne parvient pas à traiter le message avant le délai d’attente de verrou expire (par exemple, si l’application se bloque), Service Bus déverrouille le message automatiquement et le rendre disponible pour être de nouveau reçu.

Si l’application subit un incident après le traitement du message, mais avant l’appel de la méthode `deleteMessage`, le message est à nouveau remis à l’application lorsqu’elle redémarre. Cette approche est souvent appelée *au moins une fois traitement*, autrement dit, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer un traitement dupliqué, les développeurs d’applications doivent ensuite ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double. Il est souvent obtenu grâce à l’aide de la **MessageId** propriété du message, qui reste constante pendant les tentatives de remise.

> [!NOTE]
> Vous pouvez gérer les ressources de Service Bus avec [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). L’Explorateur Service Bus permet aux utilisateurs de se connecter à un espace de noms Service Bus et administrer les entités de messagerie de manière simple. L’outil fournit des fonctionnalités avancées telles que la fonctionnalité d’importation/exportation ou de la possibilité de tester une rubrique, files d’attente, abonnements, services de relais, hubs de notification et hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les files d’attente, consultez les ressources suivantes :

* [Files d’attente, rubriques et abonnements][Queues, topics, and subscriptions]
* Référentiel du [Kit de développement logiciel (SDK) Azure pour Node][Azure SDK for Node] sur GitHub
* [Centre de développement Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
