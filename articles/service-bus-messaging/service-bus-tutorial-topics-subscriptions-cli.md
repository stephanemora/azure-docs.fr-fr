---
title: 'Tutoriel : Mise à jour d’un assortiment d’inventaire de vente au détail à l’aide de canaux de publication/abonnement et de filtres de rubrique avec Azure CLI'
description: 'Tutoriel : Dans ce tutoriel, vous découvrez comment envoyer et recevoir des messages à partir d’une rubrique et d’un abonnement et comment ajouter et utiliser les règles de filtre à l’aide d’Azure CLI'
ms.date: 06/23/2020
ms.topic: tutorial
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 5340ec8d3d7a479fb349ce5ed1d32f917668d0d8
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85340993"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Tutoriel : mise à jour de l’inventaire à l’aide de CLI et des rubriques/abonnements

Microsoft Azure Service Bus est un service de messagerie cloud multi-locataire qui envoie des informations entre les applications et les services. Le fonctionnement asynchrone vous offre un service de messagerie répartie flexible, avec messagerie premier entré, premier sorti (FIFO) et fonctionnalités de publication/abonnement. Ce tutoriel montre comment utiliser les rubriques et abonnements Service Bus dans un scénario d’inventaire de vente au détail, avec des canaux de publication/abonnement à l’aide d’Azure CLI et de Java.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Créer une rubrique Service Bus et un ou plusieurs abonnements à cette rubrique à l’aide d’Azure CLI
> * Ajouter des filtres de rubrique à l’aide d’Azure CLI
> * Créer deux messages avec un contenu différent
> * Envoyer les messages et vérifiez qu’ils arrivent dans les abonnements attendus
> * Recevoir des messages des abonnements

Un exemple de ce scénario est une mise à jour d’assortiment d’inventaire pour plusieurs magasins de détail. Dans ce scénario, chaque magasin ou ensemble de magasins, reçoit les messages destinés à mettre à jour leurs assortiments. Ce tutoriel montre comment implémenter ce scénario à l’aide des filtres et des abonnements. Tout d’abord, vous créez une rubrique avec 3 abonnements, ajoutez des règles et des filtres, puis vous envoyez et recevez des messages à partir de la rubrique et des abonnements.

![topic](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Si vous n'avez pas d'abonnement Azure, vous pouvez créer un [compte gratuit][] avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour développer une application Service Bus avec Java, les éléments suivants doivent être installés :

- [Kit de développement Java](https://aka.ms/azure-jdks), dernière version.
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel exige que vous exécutiez Azure CLI version 2.0.4 ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Rubriques et abonnements Service Bus

Chaque [abonnement à une rubrique](service-bus-messaging-overview.md#topics) peut recevoir une copie de chaque message. Les rubriques sont entièrement compatibles au niveau de la sémantique et du protocole avec les files d’attente Service Bus. Les rubriques Service Bus prennent en charge un large éventail de règles de sélection avec des conditions de filtre et avec des actions facultatives qui permettent de définir ou de modifier les propriétés des messages. Chaque fois qu’une règle correspond, elle génère un message. Pour en savoir plus sur les règles, filtres et actions, consultez ce [lien](topic-filters.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Une fois que l’interface CLI est installée, ouvrez une invite de commandes et exécutez les commandes suivantes pour vous connecter à Azure. Ces étapes ne sont pas nécessaires si vous utilisez Cloud Shell :

1. Si vous utilisez l’interface de ligne de commande Azure en local, exécutez la commande suivante pour vous connecter à Azure. Cette étape de connexion n’est pas nécessaire si vous exécutez ces commandes dans Cloud Shell :

   ```azurecli-interactive
   az login
   ```

2. Définissez le contexte de l’abonnement actuel sur l’abonnement Azure que vous souhaitez utiliser :

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Utiliser CLI pour provisionner des ressources

Utilisez les commandes suivantes pour provisionner les ressources Service Bus. Veillez à remplacer tous les espaces réservés par les valeurs appropriées :

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Après l’exécution de la dernière commande, copiez et collez la chaîne de connexion et le nom de la file d’attente que vous avez sélectionnée dans un emplacement temporaire tel que le Bloc-notes. Vous en aurez besoin à l’étape suivante.

## <a name="create-filter-rules-on-subscriptions"></a>Créer des règles de filtre sur les abonnements

Une fois que l’espace de noms et les rubriques/abonnements sont configurés, et que vous disposez des informations d’identification nécessaires, vous êtes prêt à créer des règles de filtre sur les abonnements, puis à envoyer et recevoir des messages. Vous pouvez consulter le code dans [ce dossier d’exemples GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Envoyer et recevoir des messages

1. Veillez à ce que Cloud Shell soit ouvert et affiche l’invite de commandes Bash.

2. Clonez le [référentiel GitHub Service Bus](https://github.com/Azure/azure-service-bus/) en exécutant la commande suivante :

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Accédez à l’exemple de dossier `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Notez que dans l’interpréteur de commandes Bash, les commandes respectent la casse et les séparateurs de chemin d’accès doivent être des barres obliques.

3. Exécutez la commande suivante pour créer l’application :
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Pour exécuter le programme, exécutez la commande suivante. Veillez à remplacer les espaces réservés avec les noms de la chaîne de connexion et de la rubrique obtenus à l’étape précédente :

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Observez l’envoi de 10 messages à la rubrique suivi de leur réception par les abonnements individuels :

   ![sortie du programme](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Exécutez la commande suivante pour supprimer le groupe de ressources, l’espace de noms et toutes les ressources associées :

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

Cette section contient plus de détails sur ce que fait l’exemple de code.

### <a name="get-connection-string-and-queue"></a>Obtention de la chaîne de connexion et de la file d’attente

Tout d’abord, le code déclare un ensemble de variables qui déterminent l’exécution restante du programme :

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

La chaîne de connexion et le nom de la rubrique sont les seules valeurs ajoutées via les paramètres de ligne de commande et transmises à `main()`. L’exécution de code réel est déclenchée dans la méthode `run()` et envoie, puis reçoit les messages de la rubrique :

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Créer le client de rubrique pour envoyer des messages

Pour envoyer et recevoir des messages, la méthode `sendMessagesToTopic()` crée une instance de client de rubrique, qui utilise la chaîne de connexion et le nom de la rubrique, puis appelle une autre méthode qui envoie les messages :

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Recevoir des messages des abonnements individuels

La méthode `receiveAllMessages()` appelle la méthode `receiveAllMessageFromSubscription()` qui crée ensuite un client d’abonnement par appel et reçoit des messages des abonnements individuels :

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

> [!NOTE]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer permet aux utilisateurs de se connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction d’importation/exportation ou la possibilité de tester une rubrique, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez provisionné des ressources à l’aide d’Azure CLI, puis envoyé et reçu des messages à partir d’une rubrique Service Bus et de ses abonnements. Vous avez appris à :

> [!div class="checklist"]
> * Créer une rubrique Service Bus et un ou plusieurs abonnements à cette rubrique à l’aide du portail Azure
> * Ajouter des filtres de rubrique à l’aide de code .NET
> * Créer deux messages avec un contenu différent
> * Envoyer les messages et vérifiez qu’ils arrivent dans les abonnements attendus
> * Recevoir des messages des abonnements

Pour plus d’exemples d’envoi et de réception de messages, démarrez avec [les exemples Service Bus sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Passez au prochain tutoriel pour en savoir plus sur les fonctionnalités de publication/abonnement de Service Bus.

> [!div class="nextstepaction"]
> [Mise à jour de l’inventaire à l’aide de PowerShell et des rubriques/abonnements](service-bus-tutorial-topics-subscriptions-portal.md)

[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
