---
title: Utiliser Azure PowerShell pour créer une file d’attente Service Bus
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Azure PowerShell pour créer une file d’attente Service Bus. Vous allez ensuite utiliser un exemple d’application pour envoyer et recevoir des messages à partir de la file d’attente.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 890e8d3a7592a6794fd19ac28b6ca613ac7201c6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426948"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>Démarrage rapide : Utiliser Azure PowerShell pour créer une file d’attente Service Bus
Ce démarrage rapide montre comment envoyer et recevoir des messages vers et depuis une file d’attente Service Bus, en utilisant PowerShell pour créer un espace de noms de messagerie et une file d’attente au sein de cet espace de noms, et pour obtenir les informations d’identification sur cet espace de noms. La procédure montre ensuite comment envoyer et recevoir des messages depuis cette file d’attente à l’aide de la [bibliothèque .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce didacticiel, vérifiez que les éléments suivants sont installés :

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer. 
- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) ou ultérieur. Vous utilisez Visual Studio pour générer un exemple qui envoie des messages à une file d’attente et reçoit un message de celle-ci. L’exemple consiste à tester la file d’attente que vous avez créée dans le portail. 
- [Kit de développement logiciel (SDK) NET Core](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.

Pour ce démarrage rapide, vous devez disposer de la version la plus récente d’Azure PowerShell. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation et configuration d’Azure PowerShell][]. Si vous êtes familiarisé avec Azure Cloud Shell, vous pouvez l’utiliser sans installer Azure PowerShell sur votre ordinateur. Pour plus d’informations sur Azure Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell](../cloud-shell/overview.md)

## <a name="sign-in-to-azure"></a>Connexion à Azure

1. Tout d’abord, installez le module PowerShell pour Service Bus, si ce n’est pas déjà fait :

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Exécutez la commande ci-après pour vous connecter à Azure :

   ```azurepowershell-interactive
   Login-AzAccount
   ```

3. Exécutez les commandes suivantes pour définir le contexte de l’abonnement actuel, ou consulter l’abonnement actif :

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Provisionner les ressources

À l’invite de PowerShell, exécutez les commandes suivantes pour provisionner les ressources Service Bus. Veillez à remplacer tous les espaces réservés par les valeurs appropriées :

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Après l’exécution de l’applet de commande `Get-AzServiceBusKey`, copiez et collez la chaîne de connexion et le nom de la file d’attente que vous avez sélectionnée vers un emplacement temporaire, tel que le bloc-notes. Vous en aurez besoin à l’étape suivante.

## <a name="send-and-receive-messages"></a>Envoyer et recevoir des messages

Une fois que l’espace de noms et la file d’attente sont créés et que vous avez les informations d’identification nécessaires, vous êtes prêt à envoyer et recevoir des messages. Vous pouvez consulter le code dans [ce dossier d’exemples GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Pour exécuter le code, procédez comme suit :

1. Clonez le [référentiel GitHub Service Bus](https://github.com/Azure/azure-service-bus/) en exécutant la commande suivante :

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Accédez à l’exemple de dossier `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Si vous ne l’avez pas déjà fait, obtenez la chaîne de connexion à l’aide de l’applet de commande PowerShell suivante. Veillez à remplacer `my-resourcegroup`  et  `namespace-name` avec vos valeurs spécifiques : 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. À l’invite de commande PowerShell, tapez la commande suivante :

   ```shell
   dotnet build
   ```

6. Accédez au dossier `bin\Debug\netcoreapp2.0`.

7. Tapez la commande suivante pour exécuter le programme. Veillez à remplacer `myConnectionString` par la valeur que vous avez obtenu précédemment, et `myQueueName` par le nom de la file d’attente que vous avez créée :

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. Observez les 10 messages envoyés à la file d’attente puis reçus par la file d’attente :

   ![sortie du programme](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Exécutez la commande suivante pour supprimer le groupe de ressources, l’espace de noms et toutes les ressources associées :

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

Cette section contient plus de détails sur ce que fait l’exemple de code. 

### <a name="get-connection-string-and-queue"></a>Obtention de la chaîne de connexion et de la file d’attente

Les noms de chaîne de connexion et de file d’attente sont passés à la méthode `Main()` en tant qu’arguments de ligne de commande. `Main()` déclare deux variables de chaîne pour contenir les valeurs suivantes :

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
La méthode `Main()` commence ensuite la boucle de messages asynchrones, `MainAsync()`.

### <a name="message-loop"></a>Boucle de messages

La méthode MainAsync() crée un client de file d’attente avec les arguments de ligne de commande, appelle un gestionnaire de réception de messages nommé `RegisterOnMessageHandlerAndReceiveMessages()`, puis envoie l’ensemble des messages :

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

La méthode `RegisterOnMessageHandlerAndReceiveMessages()` définit simplement quelques options du gestionnaire de messages, puis appelle la méthode `RegisterMessageHandler()` du client de file d’attente, qui démarre la réception :

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Envoyer des messages

Les opérations de création et d’envoi de messages se produisent dans la méthode `SendMessagesAsync()` :

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Traitement des messages

La méthode `ProcessMessagesAsync()` accuse réception, traite et termine l’opération de réception des messages :

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

> [!NOTE]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer permet aux utilisateurs de se connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction d’importation/exportation ou la possibilité de tester une rubrique, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un espace de noms Service Bus et d’autres ressources nécessaires pour envoyer et recevoir des messages depuis une file d’attente. Pour en savoir plus sur l’écriture de code afin d’envoyer et de recevoir des messages, passez aux tutoriels de la section **Envoyer et recevoir des messages**. 

> [!div class="nextstepaction"]
> [Envoyer et recevoir des messages](service-bus-dotnet-get-started-with-queues.md)

[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Installation et configuration d’Azure PowerShell]: /powershell/azure/install-Az-ps
