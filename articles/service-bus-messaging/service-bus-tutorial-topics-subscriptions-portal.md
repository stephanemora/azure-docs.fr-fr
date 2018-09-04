---
title: Tutoriel - Mise à jour d’un assortiment d’inventaire de vente au détail à l’aide de canaux de publication/abonnement et de filtres de rubrique avec le portail Azure | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment envoyer et recevoir des messages à partir d’une rubrique et d’un abonnement et comment ajouter et utiliser les règles de filtre à l’aide de .NET
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 654cb09621837c360deccecb7778c5d467592dd1
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124212"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Tutoriel : mise à jour de l’inventaire à l’aide du portail Azure et des rubriques/abonnements

Microsoft Azure Service Bus est un service de messagerie cloud multi-locataire qui envoie des informations entre les applications et les services. Le fonctionnement asynchrone vous offre un service de messagerie répartie flexible, avec messagerie premier entré, premier sorti (FIFO) et fonctionnalités de publication/abonnement. Ce tutoriel montre comment utiliser les rubriques et abonnements Service Bus dans un scénario d’inventaire de vente au détail, avec des canaux de publication/abonnement à l’aide du portail Azure et de .NET.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une rubrique Service Bus et un ou plusieurs abonnements à cette rubrique à l’aide du portail Azure
> * Ajouter des filtres de rubrique à l’aide de code .NET
> * Créer deux messages avec un contenu différent
> * Envoyer les messages et vérifiez qu’ils arrivent dans les abonnements attendus
> * Recevoir des messages des abonnements

Un exemple de ce scénario est une mise à jour d’assortiment d’inventaire pour plusieurs magasins de détail. Dans ce scénario, chaque magasin ou ensemble de magasins, reçoit les messages destinés à mettre à jour leurs assortiments. Ce tutoriel montre comment implémenter ce scénario à l’aide des filtres et des abonnements. Tout d’abord, vous créez une rubrique avec 3 abonnements, ajoutez des règles et des filtres, puis vous envoyez et recevez des messages à partir de la rubrique et des abonnements.

![rubrique](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit][] avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vérifiez que les éléments suivants sont installés :

- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) ou ultérieur.
- [Kit de développement logiciel (SDK) NET Core](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.

## <a name="service-bus-topics-and-subscriptions"></a>Rubriques et abonnements Service Bus

Chaque [abonnement à une rubrique](service-bus-messaging-overview.md#topics) peut recevoir une copie de chaque message. Les rubriques sont entièrement compatibles au niveau de la sémantique et du protocole avec les files d’attente Service Bus. Les rubriques Service Bus prennent en charge un large éventail de règles de sélection avec des conditions de filtre et avec des actions facultatives qui permettent de définir ou de modifier les propriétés des messages. Chaque fois qu’une règle correspond, elle génère un message. Pour en savoir plus sur les règles, filtres et actions, consultez ce [lien](topic-filters.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Tout d’abord, accédez au [portail Azure][Azure portal] et connectez-vous à l’aide de votre abonnement Azure. La première étape consiste à créer un espace de noms Service Bus de type **Messagerie**.

## <a name="create-a-service-bus-namespace"></a>Création d’un espace de noms Service Bus

Un espace de noms de messagerie Service Bus fournit un conteneur d’étendue unique, référencé par son [nom de domaine complet][], dans lequel vous créez une ou plusieurs files d’attente, rubriques et abonnements. L’exemple suivant crée un espace de noms de messagerie Service Bus dans un [groupe de ressources](/azure/azure-resource-manager/resource-group-portal) nouveau ou existant :

1. Dans le volet de navigation gauche du portail, cliquez sur  **+ Créer une ressource**, puis sur **Intégration Entreprise** et sur **Service Bus**.
2. Dans la boîte de dialogue **Créer un espace de noms**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
3. Après avoir vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Standard ou Premium).
4. Dans le champ **Abonnement**, sélectionnez un abonnement Azure dans lequel créer l’espace de noms.
5. Dans le champ **Groupe de ressources** , choisissez un groupe de ressources existant dans lequel l’espace de noms sera utilisé, ou créez-en un nouveau.      
6. Dans **Emplacement**, sélectionnez le pays ou la région où votre espace de noms doit être hébergé.
7. Cliquez sur **Créer**. Le système crée l’espace de noms de service et l’active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

  ![namespace](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>Obtenir les informations d’identification de gestion

Créer un espace de noms génère automatiquement une règle de signature d’accès partagé (SAS) initiale comprenant une paire de clés primaire et secondaire qui vous offre un contrôle complet sur tous les aspects de l’espace de noms. Pour copier la règle initiale, effectuez les étapes suivantes :

1. Cliquez sur **Toutes les ressources**, puis sur le nom de l’espace de noms que vous venez de créer.
2. Dans la fenêtre Espace de noms, cliquez sur **Stratégies d’accès partagé**.
3. Dans l’écran **Stratégies d’accès partagé**, cliquez sur **RootManageSharedAccessKey**.
4. Dans la fenêtre **Stratégie : RootManageSharedAccessKey**, cliquez sur le bouton **Copier** situé en regard de **Chaîne de connexion primaire**, pour copier la chaîne de connexion dans le presse-papiers pour une utilisation ultérieure. Copiez cette valeur dans le Bloc-notes ou un autre emplacement temporaire.

    ![connection-string][connection-string]
5. Répétez l’étape précédente, en copiant et collant la valeur de **Clé primaire** dans un emplacement temporaire pour l’utiliser ultérieurement.

## <a name="create-a-topic-and-subscriptions"></a>Créer une rubrique et des abonnements

Pour créer une rubrique Service Bus, spécifiez l’espace de noms dans lequel vous souhaitez la créer. L’exemple suivant montre comment créer une rubrique sur le portail :

1. Dans le volet de navigation gauche du portail, cliquez sur **Service Bus** (si vous ne voyez pas **Service Bus**, cliquez sur **Tous les services**).
2. Cliquez sur l’espace de noms dans lequel vous souhaitez créer la rubrique.
3. Dans la fenêtre de l’espace de noms, cliquez sur **Rubriques**, puis dans la fenêtre **Rubriques**, cliquez sur **+ Rubriques**.
4. Entrez le **nom** de la rubrique et laissez les valeurs par défaut des autres valeurs.
5. En bas de la fenêtre, cliquez sur **Créer**.
6. Notez le nom de la rubrique.
7. Sélectionnez la rubrique que vous venez de créer.
8. Cliquez sur **+ Abonnement**, entrez le nom d’abonnement **S1**et laissez toutes les autres valeurs par défaut.
9. Répétez la dernière étape deux fois de plus, en créant des abonnements nommés **S2** et **S3**.

## <a name="create-filter-rules-on-subscriptions"></a>Créer des règles de filtre sur les abonnements

Une fois que l’espace de noms et les rubriques/abonnements sont configurés, et que vous disposez des informations d’identification nécessaires, vous êtes prêt à créer des règles de filtre sur les abonnements, puis à envoyer et recevoir des messages. Vous pouvez consulter le code dans [ce dossier d’exemples GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/GettingStarted\BasicSendReceiveTutorialwithFilters).

### <a name="send-and-receive-messages"></a>Envoyer et recevoir des messages

Pour exécuter le code, procédez comme suit :

1. Dans une invite de commandes ou une invite PowerShell, clonez le [référentiel GitHub Service Bus](https://github.com/Azure/azure-service-bus/) en exécutant la commande suivante :

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Accédez à l’exemple de dossier `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`.

3. Récupérez la chaîne de connexion que vous avez copié dans le bloc-notes dans la section [Obtenir les informations d’identification de gestion](#obtain-the-management-credentials) de ce tutoriel. Vous avez également besoin du nom de la rubrique que vous avez créé dans la section précédente.

4. Saisissez ensuite la commande suivante dans l’invite de commandes :

   ```shell
   dotnet build
   ```

5. Accédez au dossier `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0`.

6. Tapez la commande suivante pour exécuter le programme. Veillez à remplacer `myConnectionString` par la valeur que vous avez obtenue précédemment, et `myTopicName` par le nom de la rubrique que vous avez créée :

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Suivez d’abord les instructions dans la console pour sélectionner la création de filtres. La création de filtres implique la suppression des filtres par défaut. Lorsque vous utilisez PowerShell ou CLI, vous n’avez pas besoin de supprimer les filtres par défaut, mais si faites cela dans le code, vous devez les supprimer. Les commandes de console 1 et 3 vous aident à gérer les filtres sur les abonnements que vous avez créés précédemment :

   - Exécutez 1 : pour supprimer les filtres par défaut.
   - Exécutez 2 : pour ajouter vos propres filtres.
   - Exécutez 3 : pour supprimer vos propres filtres (facultatif). Notez que cela ne recréera pas les filtres par défaut.

    ![Affichage de la sortie de 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Après la création des filtres, vous pouvez envoyer des messages. Appuyez sur 4 et observez l’envoi de 10 messages à la rubrique :

    ![Envoi de la sortie](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Appuyez sur 5 et observez la réception des messages. Si vous n’obtenez pas 10 messages en retour, appuyez sur « m » pour afficher le menu, puis appuyez de nouveau sur 5.

    ![Recevoir la sortie](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsqu’il n’est plus nécessaire, supprimez l’espace de noms et la file d’attente. Pour ce faire, sélectionnez ces ressources sur le portail et cliquez sur **Supprimer**.

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

Cette section contient plus de détails sur ce que fait l’exemple de code.

### <a name="get-connection-string-and-topic"></a>Obtention de la chaîne de connexion et de la rubrique

Tout d’abord, le code déclare un ensemble de variables qui déterminent l’exécution restante du programme.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Les noms de la rubrique et de la chaîne de connexion sont transmis via des paramètres de ligne de commande comme indiqué, puis sont lus dans la méthode `Main()` :

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Supprimer les filtres par défaut

Lorsque vous créez un abonnement, Service Bus crée un filtre par défaut par abonnement. Ce filtre permet de recevoir tous les messages envoyés à la rubrique. Si vous souhaitez utiliser des filtres personnalisés, vous pouvez supprimer le filtre par défaut, comme indiqué dans le code suivant :

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Création de filtres

Le code suivant ajoute les filtres personnalisés définis dans ce tutoriel :

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Supprimer vos filtres personnalisés

Si vous souhaitez supprimer tous les filtres de votre abonnement, le code suivant montre comment procéder :

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Envoyer des messages

L’envoi de messages à une rubrique est similaire à l’envoi de messages à une file d’attente. Cet exemple montre comment envoyer des messages, à l’aide d’une liste des tâches et d’un traitement asynchrone :

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Recevoir des messages

Là encore, les messages sont reçus via une liste des tâches, et le code utilise le traitement par lot. Vous pouvez envoyer et recevoir à l’aide du traitement par lot, mais cet exemple ne montre que la réception par lots. En réalité, vous ne quitteriez pas la boucle, mais continueriez le bouclage et définiriez une valeur timespan supérieure, par exemple une minute. L’appel de réception au répartiteur est maintenu ouvert pendant ce laps de temps et si des messages arrivent, ils sont retournés immédiatement et un nouvel appel de réception est émis. Ce concept est appelé *interrogation longue*. Utiliser la pompe de réception que vous pouvez visualiser dans le [démarrage rapide](service-bus-quickstart-portal.md), et dans plusieurs autres exemples dans le référentiel, est une option plus classique.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez provisionné des ressources à l’aide du portail Azure, puis envoyé et reçu des messages à partir d’une rubrique Service Bus et de ses abonnements. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une rubrique Service Bus et un ou plusieurs abonnements à cette rubrique à l’aide du portail Azure
> * Ajouter des filtres de rubrique à l’aide de code .NET
> * Créer deux messages avec un contenu différent
> * Envoyer les messages et vérifiez qu’ils arrivent dans les abonnements attendus
> * Recevoir des messages des abonnements

Pour plus d’exemples d’envoi et de réception de messages, démarrez avec [les exemples Service Bus sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Passez au prochain tutoriel pour en savoir plus sur les fonctionnalités de publication/abonnement de Service Bus.

> [!div class="nextstepaction"]
> [Mise à jour de l’inventaire à l’aide de PowerShell et des rubriques/abonnements](service-bus-tutorial-topics-subscriptions-powershell.md)

[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[nom de domaine complet]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png