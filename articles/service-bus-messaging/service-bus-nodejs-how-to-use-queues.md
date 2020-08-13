---
title: Utiliser des files d’attente Azure Service Bus dans Node.js à l’aide du package azure-sb
description: Découvrez comment créer des applications Node.js pour envoyer/recevoir des messages vers/en provenance d’une file d’attente Azure Service Bus en utilisant le package azure-sb.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-javascript
ms.openlocfilehash: ba0fd6cf7214beeaca9e93b1fe003a144c247cb5
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88076997"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Démarrage rapide : Utiliser les files d’attente Service Bus dans Azure avec Node.js et le package azure-sb
Dans ce tutoriel, vous allez apprendre à créer des applications Node.js pour envoyer/recevoir des messages vers/en provenance d’une file d’attente Azure Service Bus en utilisant le package [azure-sb](https://www.npmjs.com/package/azure-sb). Les exemples sont écrits en JavaScript et utilisent le [module Azure](https://www.npmjs.com/package/azure) Node.js, qui utilise en interne le package azure-sb.

> [!IMPORTANT]
> Le package [azure-sb](https://www.npmjs.com/package/azure-sb) utilise les [API d’exécution REST Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Vous pouvez bénéficier d’une expérience plus rapide en utilisant le nouveau package [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus), qui exploite le [protocole AMQP 1.0](service-bus-amqp-overview.md) plus rapide. 
> 
> Pour en savoir plus sur le nouveau package, consultez [Comment utiliser les files d’attente Service Bus avec Node.js et le @azure/service-buspackage](./service-bus-nodejs-how-to-use-queues-new-package.md), sinon poursuivez votre lecture pour savoir comment utiliser le package [azure](https://www.npmjs.com/package/azure).

## <a name="prerequisites"></a>Prérequis
- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez [activer les avantages de votre abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou [vous inscrire pour un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Si vous n’avez pas de file d’attente à utiliser, suivez les étapes de l’article [Utiliser le portail Azure pour créer une file d’attente Service Bus](service-bus-quickstart-portal.md) pour créer une file d’attente.
    1. Consultez la **vue d’ensemble** rapide des **files d’attente** Service Bus. 
    2. Créez un **espace de noms** Service Bus. 
    3. Obtenez la **chaîne de connexion**. 

        > [!NOTE]
        > Grâce à ce tutoriel, vous allez créer une **file d’attente** dans l’espace de noms Service Bus à l’aide de Node.js. 
 

## <a name="create-a-nodejs-application"></a>Création d’une application Node.js
Créez une application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, voir les pages [Création et déploiement d’une application Node.js sur un site web Azure][Create and deploy a Node.js application to an Azure Website] ou [Service cloud Node.js][Node.js Cloud Service] avec Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l’utilisation de Service Bus
Pour utiliser Azure Service Bus, téléchargez et utilisez le package Azure Node.js. Ce dernier inclut des bibliothèques permettant de communiquer avec les services REST de Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilisation de Node Package Manager (NPM) pour obtenir le package
1. Utilisez la fenêtre de commande **Windows PowerShell for Node.js** pour accéder au dossier **c:\\node\\sbqueues\\WebRole1** dans lequel vous avez créé votre exemple d’application.
2. Entrez **npm install azure** dans la fenêtre de commande, ce qui génère un résultat du type :

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
3. Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node_modules** a été créé. Dans ce dossier, recherchez le package **azure**, qui contient les bibliothèques nécessaires pour accéder aux files d’attente Service Bus.

### <a name="import-the-module"></a>Importation du module
À l’aide d’un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l’application :

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configuration d’une connexion Service Bus Azure
Le module Azure lit la variable d’environnement `AZURE_SERVICEBUS_CONNECTION_STRING` pour obtenir les informations nécessaires pour se connecter à Service Bus. Si cette variable d’environnement n’est pas définie, vous devez spécifier les informations de compte lors de l’appel de `createServiceBusService`.

Pour un exemple de configuration des variables d’environnement dans le [portail Azure][Azure portal] pour un site web Azure, consultez [Application web Node.js avec stockage][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Créer une file d’attente
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
Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées par le biais de **ServiceBusService**. Il peut s’agir d’opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

```javascript
function handle (requestOptions, next)
```

Après le prétraitement des options de la requête, la méthode doit appeler `next` en passant un rappel avec la signature suivante :

```javascript
function (returnObject, finalCallback, next)
```

Dans ce rappel, et après le traitement de `returnObject` (réponse à la requête du serveur), le rappel doit appeler `next`, s’il existe, pour continuer à traiter d’autres filtres, ou appeler `finalCallback` pour terminer l’appel du service.

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

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une file d’attente n’est pas limitée par le nombre de messages qu’elle peut contenir, elle l’est en revanche par la taille totale des messages qu’elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Réception des messages d'une file d'attente
La méthode `receiveQueueMessage` de l’objet **ServiceBusService** permet de recevoir les messages d’une file d’attente. Par défaut, les messages sont supprimés de la file d’attente au fur et à mesure de leur lecture. Cependant, vous pouvez lire et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre facultatif `isPeekLock` sur **true**.

Le comportement de lecture et de suppression du message par défaut dans le cadre de l’opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application peut tolérer le non-traitement d’un message après un échec. Pour mieux comprendre ce comportement, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Puisque Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

Si le paramètre `isPeekLock` est défini sur **true**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications incapables de tolérer des messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode `deleteMessage` et en fournissant le message à supprimer sous la forme d’un paramètre. La méthode `deleteMessage` marque le message comme étant utilisé et le supprime de la file d’attente.

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
Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode `unlockMessage` pour l’objet **ServiceBusService**. Service Bus déverrouille alors le message dans la file d’attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans une file d’attente est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’appel de la méthode `deleteMessage`, le message est à nouveau remis à l’application lorsqu’elle redémarre. Dans ce type d’approche, souvent appelée *Au moins une fois*, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, l’ajout d’une logique supplémentaire à vos applications pour traiter la remise de messages en double est recommandé. Pour ce faire, il suffit souvent d’utiliser la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

> [!NOTE]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer permet aux utilisateurs de se connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction d’importation/exportation ou la possibilité de tester une rubrique, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les files d’attente, consultez les ressources suivantes :

* [Files d’attente, rubriques et abonnements.][Queues, topics, and subscriptions]
* Référentiel du [Kit de développement logiciel (SDK) Azure pour Node][Azure SDK for Node] sur GitHub
* [Centre de développement Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/quickstart-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md