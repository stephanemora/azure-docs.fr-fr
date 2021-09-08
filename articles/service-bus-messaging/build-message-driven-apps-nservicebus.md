---
title: Créez des applications pilotées par les messages avec NServiceBus et Azure Service Bus
description: Découvrez comment résoudre des problèmes complexes liés aux systèmes distribués sur Azure Service Bus à l’aide de l’infrastructure NServiceBus.
author: kbaley
ms.author: spelluru
ms.service: service-bus-messaging
ms.topic: how-to
ms.date: 07/26/2021
ms.custom: template-how-to
ms.openlocfilehash: c9f503cb600c87e1dac590dcbbe7edf46bc7be76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122523986"
---
# <a name="build-message-driven-business-applications-with-nservicebus-and-azure-service-bus"></a>Créez des applications commerciales pilotées par les messages avec NServiceBus et Azure Service Bus
NServiceBus est une infrastructure de messagerie commerciale fournie par un Logiciel particulier. Elle s’appuie sur Azure Service Bus et aide les développeurs à se concentrer sur la logique commerciale en faisant abstraction des problèmes d’infrastructure. Dans ce guide, nous allons créer une solution qui échange des messages entre deux services. Nous vous montrerons également comment réessayer automatiquement les messages ayant échoué et passer en revue les options d’hébergement de ces services dans Azure.

> [!NOTE]
> Le code de ce didacticiel est disponible sur le [site Web des documents logiciels particuliers](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/). 

## <a name="prerequisites"></a>Configuration requise

Cet exemple suppose que vous avez [créé un espace de noms Azure Service Bus](service-bus-create-namespace-portal.md).

> [!IMPORTANT]
> NServiceBus requiert au moins le niveau Standard. Le niveau de Base ne fonctionnera pas.

## <a name="download-and-prepare-the-solution"></a>Télécharger et préparer la solution
1. Téléchargez le code à partir du [site Web des documents logiciels particuliers](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/). La solution `SendReceiveWithNservicebus.sln` comprend trois projets :

    - **Expéditeur** : application console qui envoie des messages
    - **Destinataire** : application console qui reçoit les messages de l’expéditeur et renvoie les réponses
    - **Partagé** : bibliothèque de classes contenant les contrats de message partagés entre l’expéditeur et le destinataire
    
    Le diagramme suivant, généré par [ServiceInsight](https://particular.net/serviceinsight), un outil de visualisation et de débogage à partir d’un Logiciel particulier, illustre le flux des messages :
    
    :::image type="content" source="./media/nservicebus/sequence-diagram.png" alt-text="Image montrant le diagramme de séquence":::    
1. Ouvrez `SendReceiveWithNservicebus.sln` dans l’éditeur de code de votre choix (par exemple, Visual Studio 2019). 
1. Ouvrez `appsettings.json` dans les projets Destinataire et Expéditeur et définissez `AzureServiceBusConnectionString` sur la chaîne de connexion pour votre espace de noms Azure Service Bus.



## <a name="define-the-shared-message-contracts"></a>Définir les contrats de message partagés

La bibliothèque de classes partagée vous permet de définir les contrats utilisés pour envoyer nos messages. Elle inclut une référence au `NServiceBus` package NuGet, qui contient des interfaces que vous pouvez utiliser pour identifier nos messages. Les interfaces ne sont pas requises, mais elles nous permettent d’effectuer une validation supplémentaire à partir de NServiceBus et de permettre au code d’être auto-documenté.

En premier, nous évaluerons la classe `Ping.cs`

```csharp
public class Ping : NServiceBus.ICommand
{
    public int Round { get; set; }
}
```

La classe `Ping` définit un message que l’Expéditeur envoie au Destinataire. Il s’agit d’une classe C# simple qui implémente `NServiceBus.ICommand` , une interface à partir du package Nservicebus. Ce message signale au lecteur et à NServiceBus qu’il s’agit d’une commande, bien qu’il existe d’autres façons d’identifier des messages [sans utiliser d’interfaces](https://docs.particular.net/nservicebus/messaging/conventions).

L’autre classe de message dans les Projets partagés est `Pong.cs` :

```csharp
public class Pong : NServiceBus.IMessage
{
    public string Acknowledgement { get; set; }
}
```

`Pong` est également un objet C# simple, bien que celui-ci implémente `NServiceBus.IMessage`. L'`IMessage`interface représente un message générique qui n’est ni une commande ni un événement, et qui est couramment utilisé pour les réponses. Dans notre exemple, il s’agit d’une réponse que le Destinataire renvoie à l’Expéditeur pour indiquer qu’un message a été reçu.

Le `Ping` et le `Pong` sont les deux types de message que vous utiliserez. L’étape suivante consiste à configurer l’Expéditeur pour qu’il utilise Azure Service Bus et envoie un `Ping` message.

## <a name="set-up-the-sender"></a>Configurer l’expéditeur

L’Expéditeur est un point de terminaison qui envoie notre `Ping` message. Ici, vous configurez l’Expéditeur pour qu’il utilise Azure Service Bus comme mécanisme de transport, construise une `Ping` instance et l’envoie.

Dans la `Main` méthode de `Program.cs`, vous configurez le point de terminaison de l’Expéditeur :

```csharp
var host = Host.CreateDefaultBuilder(args)
    // Configure a host for the endpoint
    .ConfigureLogging((context, logging) =>
    {
        logging.AddConfiguration(context.Configuration.GetSection("Logging"));

        logging.AddConsole();
    })
    .UseConsoleLifetime()
    .UseNServiceBus(context =>
    {
        // Configure the NServiceBus endpoint
        var endpointConfiguration = new EndpointConfiguration("Sender");

        var transport = endpointConfiguration.UseTransport<AzureServiceBusTransport>();
        var connectionString = context.Configuration.GetConnectionString("AzureServiceBusConnectionString");
        transport.ConnectionString(connectionString);

        transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

        endpointConfiguration.EnableInstallers();
        endpointConfiguration.AuditProcessedMessagesTo("audit");

        return endpointConfiguration;
    })
    .ConfigureServices(services => services.AddHostedService<SenderWorker>())
    .Build();

await host.RunAsync();
```

Il y a beaucoup à déballer ici, nous allons donc procéder étape par étape.

### <a name="configure-a-host-for-the-endpoint"></a>Configurer un hôte pour le point de terminaison

L’hébergement et la journalisation sont configurés à l’aide des [options d'Hôte générique Microsoft](/dotnet/core/extensions/generic-host) standard. Pour le moment, le point de terminaison est configuré pour s’exécuter en tant qu’application console, mais il peut être modifié pour s’exécuter dans Azure Functions avec des modifications minimes, que nous aborderons plus loin dans cet article.

### <a name="configure-the-nservicebus-endpoint"></a>Configurer le point de terminaison NServiceBus

Ensuite, vous indiquerez à l’hôte d’utiliser NServiceBus avec la `.UseNServiceBus(…)` méthode d’extension. La méthode prend une fonction de rappel qui retourne un point de terminaison qui démarrera lors de l’exécution de l’hôte.

Dans la configuration du point de terminaison, vous spécifiez `AzureServiceBus` pour notre transport, en fournissant une chaîne de connexion à partir de `appsettings.json`. . Ensuite, vous allez configurer le routage afin que les messages de type `Ping` soient envoyés à un point de terminaison nommé « Destinataire ». Il permet à NServiceBus d’automatiser le processus de distribution du message vers la destination sans nécessiter l’adresse du destinataire.

L’appel à `EnableInstallers` va configurer notre topologie dans l’espace de noms Azure Service Bus lorsque le point de terminaison est lancé, en créant les files d’attente requises, le cas échéant. Dans des environnements de production, [la création de scripts opérationnels](https://docs.particular.net/transports/azure-service-bus/operational-scripting) est une autre option pour créer la topologie.

### <a name="set-up-background-service-to-send-messages"></a>Configurer le service en arrière-plan pour envoyer des messages

La pièce finale de l’expéditeur est `SenderWorker`, un service en arrière-plan qui est configuré pour envoyer un `Ping` message chaque seconde.

```csharp
public class SenderWorker : BackgroundService
{
    private readonly IMessageSession messageSession;
    private readonly ILogger<SenderWorker> logger;

    public SenderWorker(IMessageSession messageSession, ILogger<SenderWorker> logger)
    {
        this.messageSession = messageSession;
        this.logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        try
        {
            var round = 0;
            while (!stoppingToken.IsCancellationRequested)
            {
                await messageSession.Send(new Ping { Round = round++ })
                    .ConfigureAwait(false);

                logger.LogInformation($"Message #{round}");

                await Task.Delay(1_000, stoppingToken)
                    .ConfigureAwait(false);
            }
        }
        catch (OperationCanceledException)
        {
            // graceful shutdown
        }
    }
}
```

Le `IMessageSession` utilisé dans `ExecuteAsync` est injecté dans `SenderWorker` et nous permet d’envoyer des messages à l’aide de NServiceBus en dehors du gestionnaire de messages. Le routage que vous avez configuré dans `Sender` précise la destination des `Ping` messages. Il conserve la topologie du système (tels messages sont acheminés vers telles adresses) comme un élément distinct du code d’entreprise.

L’application de l’Expéditeur contient également un `PongHandler`. Vous y reviendrez après que nous ayons parlé du Destinataire, ce que nous allons faire à la suite.

## <a name="set-up-the-receiver"></a>Définir le destinataire

Le Destinataire est un point de terminaison qui écoute un `Ping` message, enregistre la réception d’un message et répond à l’expéditeur. Dans cette section, nous examinerons rapidement la configuration du point de terminaison, qui est similaire à l’Expéditeur, puis nous nous intéresserons au gestionnaire de messages.

Comme l’expéditeur, définissez le récepteur comme une application console à l’aide de l’Hôte générique Microsoft. Il utilise la même configuration de journalisation et de point de terminaison (avec Azure Service Bus comme transport de messages), mais avec un nom différent, pour le distinguer de l’expéditeur :

```csharp
var endpointConfiguration = new EndpointConfiguration("Receiver");
```

Étant donné que ce point de terminaison répond uniquement à son donneur d’ordre et ne démarre pas de nouvelles conversations, aucune configuration de routage n’est requise. En outre, il n’a pas besoin d’un travail d’arrière-plan comme l’Expéditeur, car il répond uniquement lorsqu’il reçoit un message.

### <a name="the-ping-message-handler"></a>Le gestionnaire de messages Ping

Le projet du Destinataire dispose d’un _gestionnaire de messages_ appelé`PingHandler` :

```csharp
public class PingHandler : NServiceBus.IHandleMessages<Ping>
{
    private readonly ILogger<PingHandler> logger;

    public PingHandler(ILogger<PingHandler> logger)
    {
        this.logger = logger;
    }

    public async Task Handle(Ping message, IMessageHandlerContext context)
    {
        logger.LogInformation($"Processing Ping message #{message.Round}");

        // throw new Exception("BOOM");

        var reply = new Pong { Acknowledgement = $"Ping #{message.Round} processed at {DateTimeOffset.UtcNow:s}" };

        await context.Reply(reply);
    }
}
```

Nous allons ignorer le code commenté pour l’instant. Nous y reviendrons ultérieurement quand nous aborderons la récupération après un échec.

La classe implémente `IHandleMessages<Ping>`, qui définit une méthode : `Handle`. Cette interface indique à NServiceBus que lorsque le point de terminaison reçoit un message de type `Ping`, il doit être traité par la `Handle` méthode dans ce gestionnaire. La `Handle` méthode considère le message comme un paramètre et un `IMessageHandlerContext`, qui permet d’effectuer d’autres opérations de messagerie, telles que la réponse, l’envoi de commandes ou la publication d’événements.

Notre `PingHandler`est simple : lorsqu’un `Ping` message est reçu, enregistre les détails du message et répond à l’expéditeur par un nouveau `Pong` message.

> [!NOTE]
> Dans la configuration de l’Expéditeur, vous avez spécifié que les `Ping` messages doivent être routés vers le Destinataire. NServiceBus ajoute des métadonnées aux messages indiquant, entre autres, l’origine du message. C’est la raison pour laquelle vous n’avez pas besoin de spécifier de données de routage pour le `Pong` message de réponse ; il est automatiquement redirigé vers son donneur d’ordre : l’Expéditeur.
>

Une fois l’Expéditeur et le Destinataire correctement configurés, vous pouvez maintenant exécuter la solution.

## <a name="run-the-solution"></a>Exécuter la solution

Pour lancer la solution, vous devez exécuter à la fois l’Expéditeur et le Destinataire. Si vous utilisez Visual Studio Code, lancez la configuration « Déboguer tout ». Si vous utilisez Visual Studio, configurez la solution pour lancer les projets Expéditeur et Destinataire :

1. Cliquez avec le bouton droit sur la solution dans l'Explorateur de solutions
1. Sélectionnez « Définir les projets de démarrage...»
1. Sélectionnez **Plusieurs projets de démarrage**
1. Pour l’Expéditeur et le Destinataire, sélectionnez « Démarrer » dans la liste déroulante

Lancez la solution. Deux applications console s’affichent : une pour l’Expéditeur et une pour le Destinataire.

# <a name="sender"></a>[Expéditeur](#tab/Sender)

:::image type="content" source="./media/nservicebus/sender.png" alt-text="Image représentant le point de terminaison qui envoie des messages Ping au Destinataire":::

# <a name="receiver"></a>[Destinataire](#tab/Receiver)

:::image type="content" source="./media/nservicebus/receiver.png" alt-text="Image représentant le point de terminaison qui reçoit les messages Ping de l’Expéditeur":::

---

Dans l’Expéditeur, notez qu’un `Ping` message est envoyé toutes les secondes, grâce à la `SenderWorker` tâche en arrière-plan. Le Destinataire affiche les détails de chaque `Ping` message reçu et l’Expéditeur consigne les détails de chaque `Pong` message qu’il reçoit dans la réponse.

Maintenant que tout fonctionne, c’est parti.

## <a name="resilience-in-action"></a>Résilience en action

Les erreurs sont inévitables dans les systèmes logiciels. Il est inévitable que le code échoue et qu’il puisse le faire pour diverses raisons, telles que les défaillances du réseau, les verrous de base de données, les modifications apportées à une API tierce et les erreurs de code ordinaires.

NServiceBus offre de puissantes fonctionnalités de récupération pour la gestion des défaillances. Lorsqu’un gestionnaire de messages échoue, les messages sont automatiquement retentés en fonction d’une stratégie prédéfinie. Il existe deux types de stratégie de nouvelle tentative : les nouvelles tentatives immédiates et les nouvelles tentatives retardées. La meilleure façon de décrire comment elles fonctionnent consiste à les voir en action. Ajoutons une stratégie de nouvelle tentative au point de terminaison de notre Destinataire :

1. Ouvrez `Program.cs` dans le projet Expéditeur
1. Après la ligne `.EnableInstallers`, ajoutez le code suivant :

```csharp
endpointConfiguration.SendFailedMessagesTo("error");
var recoverability = endpointConfiguration.Recoverability();
recoverability.Immediate(
    immediate =>
    {
        immediate.NumberOfRetries(3);
    });
recoverability.Delayed(
    delayed =>
    {
        delayed.NumberOfRetries(2);
        delayed.TimeIncrease(TimeSpan.FromSeconds(5));
    });
```

Avant d’aborder le fonctionnement de cette stratégie, voyons-la en action. Avant de tester la stratégie de récupération, vous devez simuler une erreur. Ouvrez le code `PingHandler` dans le projet Destinataire et supprimez les marques de commentaire de la ligne suivante :

```csharp
throw new Exception("BOOM");
```

Désormais, lorsque le Destinataire gèrera un message `Ping`, il échouera. Lancez à nouveau la solution et voyons ce qui se passe dans le Destinataire. 

Avec notre `PingHandler` le moins fiable, tous nos messages échoueront. Vous pouvez voir que la stratégie de nouvelle tentative démarre pour ces messages. La première fois qu’un message échoue, il est immédiatement relancé jusqu’à trois fois :

:::image type="content" source="./media/nservicebus/immediate-retries.png" alt-text="Image indiquant la stratégie de nouvelle tentative immédiate qui réessaie les messages jusqu’à 3 fois":::

Bien entendu, il continuera à échouer si les trois nouvelles tentatives immédiates sont utilisées, la stratégie de nouvelle tentative retardée s’exécute et le message est retardé de 5 secondes :

:::image type="content" source="./media/nservicebus/delayed-retries.png" alt-text="Image indiquant la stratégie de nouvelle tentative retardée qui retarde les messages par incréments de 5 secondes avant de tenter un autre cycle de nouvelles tentatives immédiates":::

 Une fois ces 5 secondes écoulées, le message est à nouveau retenté à trois reprises (autrement dit, une autre itération de la stratégie de nouvelle tentative immédiate). Ils échoueront également et NServiceBus retardera de nouveau le message, cette fois-ci pendant 10 secondes, avant de réessayer.

Si `PingHandler` ne réussit toujours pas après exécution de la stratégie de nouvelle connexion complète, le message est placé dans une file d’attente _centralisée_ appelée `error`, telle que définie par l’appel à `SendFailedMessagesTo`. 

:::image type="content" source="./media/nservicebus/failed-message.png" alt-text="Image indiquant le message ayant échoué":::

Le concept d’une file d’attente d’erreurs centralisée diffère du mécanisme de lettres mortes dans Azure Service Bus, lequel possède une file d’attente de lettres mortes pour chaque file d’attente de traitement. Avec NServiceBus, les files d’attente de lettres mortes dans Azure Service Bus agissent comme de véritables files d’attente de messages incohérents, alors que les messages qui finissent dans la file d’attente d’erreurs centralisée peuvent être retraités ultérieurement, si nécessaire.

La stratégie de nouvelles tentatives permet de traiter [différents types d’erreurs](https://particular.net/blog/but-all-my-errors-are-severe), lesquelles sont souvent temporaires ou semi-temporaires par nature. Autrement dit, des erreurs qui sont temporaires et disparaissent souvent si le message est simplement retraité après un bref délai. Par exemple, les défaillances réseau, les verrous de base de données et les interruptions d’API tierces.

Une fois qu’un message est dans la file d’attente des erreurs, vous pouvez examiner les détails du message dans l’outil de votre choix, puis décider de l’action à effectuer. Par exemple, en utilisant [ServicePulse](https://particular.net/servicepulse), un outil d’analyse par Logiciel particulier, nous pouvons afficher les détails du message et la raison de l’échec :

:::image type="content" source="./media/nservicebus/servicepulse.png" alt-text="Image représentant ServicePulse, à partir d’un Logiciel particulier":::

Après avoir examiné les détails, vous pouvez renvoyer le message à sa file d’attente d’origine pour traitement. Vous pouvez également modifier le message avant de le faire. S’il existe plusieurs messages dans la file d’attente des erreurs, qui ont échoué pour la même raison, ils peuvent tous être renvoyés à leurs destinations d’origine sous forme de lot.

Ensuite, il est temps de déterminer où déployer notre solution dans Azure.

## <a name="where-to-host-the-services-in-azure"></a>Emplacement d’hébergement des services dans Azure

Dans cet exemple, les points de terminaison de l’Expéditeur et du Destinataire sont configurés pour s’exécuter en tant qu’applications console. Ils peuvent également être hébergés dans différents services Azure, notamment Azure Functions, Azure App Services, les Instances de conteneur Azure, les Services Kubernetes Azure et les machines virtuelles Azure. Par exemple, voici comment le point de terminaison de l’Expéditeur peut être configuré pour s’exécuter en tant que Fonction Azure :

```csharp
[assembly: FunctionsStartup(typeof(Startup))]
[assembly: NServiceBusEndpointName("Sender")]

public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.UseNServiceBus(() =>
        {
            var configuration = new ServiceBusTriggeredEndpointConfiguration("Sender");
            var transport = configuration.AdvancedConfiguration.Transport;
            transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

            return configuration;
        });
    }
}
```

Pour plus d’informations sur l’utilisation de NServiceBus avec les Fonctions, consultez [Fonctions Azure avec Azure Service Bus](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus) dans la documentation de NServiceBus.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de NServiceBus avec les services Azure, consultez les articles suivants :

- [Exemple Envoyer/Répondre Azure Service Bus](https://docs.particular.net/samples/azure-service-bus-netstandard/send-reply/)
- [Utilisation de NServiceBus avec les Fonctions Azure](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)
- [transport Azure Service Bus sur NServiceBus](https://docs.particular.net/transports/azure-service-bus/)
- [NServiceBus et Azure](https://docs.particular.net/nservicebus/azure/)
- [NServiceBus](https://particular.net/nservicebus)
- [Didacticiel de Démarrage rapide NServiceBus](https://docs.particular.net/tutorials/quickstart)
