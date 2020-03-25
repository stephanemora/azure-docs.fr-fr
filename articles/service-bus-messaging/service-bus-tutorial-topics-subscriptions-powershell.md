---
title: 'Didacticiel : Mise à jour d’un assortiment d’inventaire de vente au détail à l’aide de canaux de publication/abonnement et de filtres de rubrique avec Azure PowerShell | Microsoft Docs'
description: Dans ce tutoriel, vous découvrez comment envoyer et recevoir des messages à partir d’une rubrique et d’un abonnement et comment ajouter et utiliser les règles de filtre à l’aide d’Azure PowerShell
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 3d1e1491ad045eba88ca7bbe54a1acb38199f7d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "65987912"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Tutoriel : mise à jour de l’inventaire à l’aide de PowerShell et des rubriques/abonnements

Microsoft Azure Service Bus est un service de messagerie cloud multi-locataire qui envoie des informations entre les applications et les services. Le fonctionnement asynchrone vous offre un service de messagerie répartie flexible, avec messagerie premier entré, premier sorti (FIFO) et fonctionnalités de publication/abonnement. 

Ce tutoriel montre comment envoyer et recevoir des messages vers et depuis une file d’attente Service Bus, en utilisant PowerShell pour créer un espace de noms de messagerie et une file d’attente au sein de cet espace de noms, et pour obtenir les informations d’identification sur cet espace de noms. La procédure montre ensuite comment envoyer et recevoir des messages depuis cette file d’attente à l’aide de la [bibliothèque .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Créer une rubrique Service Bus et un ou plusieurs abonnements à cette rubrique à l’aide d’Azure PowerShell
> * Ajouter des filtres de rubrique à l’aide de PowerShell
> * Créer deux messages avec un contenu différent
> * Envoyer les messages et vérifiez qu’ils arrivent dans les abonnements attendus
> * Recevoir des messages des abonnements

Un exemple de ce scénario est une mise à jour d’assortiment d’inventaire pour plusieurs magasins de détail. Dans ce scénario, chaque magasin ou ensemble de magasins, reçoit les messages destinés à mettre à jour leurs assortiments. Ce tutoriel montre comment implémenter ce scénario à l’aide des filtres et des abonnements. Tout d’abord, vous créez une rubrique avec 3 abonnements, ajoutez des règles et des filtres, puis vous envoyez et recevez des messages à partir de la rubrique et des abonnements.

![topic](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce didacticiel, vérifiez que les éléments suivants sont installés :

1. [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) ou ultérieur.
2. [Kit de développement logiciel (SDK) NET Core](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.

Pour ce tutoriel, vous devez disposer de la version la plus récente d’Azure PowerShell. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation et configuration d’Azure PowerShell][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure, exécutez les commandes ci-après. Ces étapes ne sont pas nécessaires si vous exécutez des commandes PowerShell dans Cloud Shell : 

1. Installez le module Service Bus PowerShell :

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Exécutez la commande ci-après pour vous connecter à Azure :

   ```azurepowershell-interactive
   Login-AzAccount
   ```

4. Définissez le contexte de l’abonnement actuel, ou consultez l’abonnement actif :

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Provisionner les ressources

Une fois connecté à Azure, utilisez les commandes ci-après pour approvisionner les ressources Service Bus. Veillez à remplacer tous les espaces réservés par les valeurs appropriées :

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Après l’exécution de la cmdlet `Get-AzServiceBusKey`, copiez et collez la chaîne de connexion et le nom de la file d’attente que vous avez sélectionnée vers un emplacement temporaire, tel que le bloc-notes. Vous en aurez besoin à l’étape suivante.

## <a name="send-and-receive-messages"></a>Envoyer et recevoir des messages

Une fois que l’espace de noms et la file d’attente sont créés et que vous avez les informations d’identification nécessaires, vous êtes prêt à envoyer et recevoir des messages. Vous pouvez consulter le code dans [ce dossier d’exemples GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Pour exécuter le code, procédez comme suit :

1. Clonez le [référentiel GitHub Service Bus](https://github.com/Azure/azure-service-bus/) en exécutant la commande suivante :

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Ouvrez une invite PowerShell.

3. Accédez à l’exemple de dossier `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Si vous ne l’avez pas déjà fait, obtenez la chaîne de connexion à l’aide de l’applet de commande PowerShell suivante. Veillez à remplacer `my-resourcegroup` et `namespace-name` avec vos valeurs spécifiques : 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. À l’invite de commande PowerShell, tapez la commande suivante :

   ```shell
   dotnet build
   ```
6. Accédez au dossier `\bin\Debug\netcoreapp2.0`.
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

La méthode `MainAsync()` crée un client de file d’attente avec les arguments de ligne de commande, appelle un gestionnaire de réception de messages nommé `RegisterOnMessageHandlerAndReceiveMessages()`, puis envoie l’ensemble des messages :

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

La méthode `RegisterOnMessageHandlerAndReceiveMessages()` définit quelques options du gestionnaire de messages, puis appelle la méthode `RegisterMessageHandler()` du client de file d’attente ), qui démarre la réception :

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

Dans ce tutoriel, vous avez provisionné des ressources à l’aide d’Azure PowerShell, puis envoyé et reçu des messages à partir d’une rubrique Service Bus et de ses abonnements. Vous avez appris à :

> [!div class="checklist"]
> * Créer une rubrique Service Bus et un ou plusieurs abonnements à cette rubrique à l’aide du portail Azure
> * Ajouter des filtres de rubrique à l’aide de code .NET
> * Créer deux messages avec un contenu différent
> * Envoyer les messages et vérifiez qu’ils arrivent dans les abonnements attendus
> * Recevoir des messages des abonnements

Pour plus d’exemples d’envoi et de réception de messages, démarrez avec [les exemples Service Bus sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Passez au prochain tutoriel pour en savoir plus sur les fonctionnalités de publication/abonnement de Service Bus.

> [!div class="nextstepaction"]
> [Mise à jour de l’inventaire à l’aide de PowerShell et des rubriques/abonnements](service-bus-tutorial-topics-subscriptions-cli.md)

[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Installation et configuration d’Azure PowerShell]: /powershell/azure/install-Az-ps
