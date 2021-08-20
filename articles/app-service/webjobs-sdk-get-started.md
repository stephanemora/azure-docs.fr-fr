---
title: Tutoriel pour le traitement en arrière-plan basé sur les événements avec le Kit de développement logiciel (SDK) WebJobs
description: Découvrez comment permettre à vos applications web d’exécuter des tâches en arrière-plan. Utilisez ce tutoriel pour vous familiariser avec le Kit de développement logiciel (SDK) WebJobs.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.date: 06/25/2021
ms.author: glenga
ms.topic: tutorial
ms.openlocfilehash: 4a017d57062e2871052305eb9bbf393c808a643d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114293281"
---
# <a name="tutorial-get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Tutoriel : Prise en main du Kit de développement logiciel (SDK) Azure WebJobs pour le traitement en arrière-plan basé sur les événements

Prise en main du Kit de développement logiciel (SDK) Azure WebJobs pour Azure App Service pour permettre à vos applications web d’exécuter des tâches en arrière-plan et des tâches planifiées et de répondre à des événements. 

Utilisez Visual Studio 2019 pour créer une application console .NET Core qui utilise le Kit de développement logiciel (SDK) WebJobs pour répondre aux messages de la file d’attente de Stockage Azure, exécuter le projet localement et le déployer sur Azure.

Dans ce didacticiel, vous apprendrez à :

> [!div class="checklist"]
> * Créer une application console
> * Ajouter une fonction
> * Tester les topologies localement
> * Déployer dans Azure
> * Activer la journalisation dans Application Insights
> * Ajouter des liaisons d’entrée/sortie

## <a name="prerequisites"></a>Prérequis

* Visual Studio 2019 avec la charge de travail de **développement Azure**. [Installez Visual Studio 2019](/visualstudio/install/).

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet).

## <a name="create-a-console-app"></a>Créer une application console
Dans cette section, vous commencez par créer un projet dans Visual Studio 2019. Ensuite, vous ajouterez des outils pour le développement Azure, la publication de code et des fonctions qui écoutent les déclencheurs et appellent des fonctions. Enfin, vous allez configurer la journalisation de la console qui désactive un outil d’analyse hérité et active un fournisseur de console avec un filtrage par défaut. 

>[!NOTE]  
>Les procédures de cet article sont vérifiées pour créer une application console .NET Core qui s’exécute sur .NET Core 3.1.

### <a name="create-a-project"></a>Création d’un projet

1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Sous **Créer un projet**, sélectionnez **Application console (C#)** , puis cliquez sur **Suivant**.

1. Sous **Configurer votre nouveau projet**, nommez le projet *WebJobsSDKSample*, puis sélectionnez **Suivant**.

1. Choisissez votre **version cible de .Net Framework** et sélectionnez **Créer**. Ce tutoriel a été vérifié avec .NET Core 3.1.

### <a name="install-webjobs-nuget-packages"></a>Installer des packages NuGet WebJobs

Installez le package NuGet WebJobs le plus récent. Ce package comprend Microsoft.Azure.WebJobs (Kit de développement logiciel [SDK] WebJobs), qui vous permet de publier votre code de fonction sur WebJobs dans Azure App Service.

1. Obtenez la version 3.x stable la plus récente du [package NuGet Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/).

2. Dans Visual Studio, accédez à **Outils** > **Gestionnaire de package NuGet**.

3. Sélectionnez **Console du gestionnaire de package**. Vous verrez une liste de cmdlets NuGet, un lien vers la documentation et un point d’entrée `PM>`.

4. Dans la commande suivante, remplacez `<3_X_VERSION>` par le numéro de la version actuelle que vous avez trouvé à l’étape 1. 

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```
5. Dans la **console du gestionnaire de package**, exécutez la commande. La liste d’extensions s’affiche et s’installe automatiquement. 
  
### <a name="create-the-host"></a>Créer l’hôte

L’hôte est le conteneur d’exécution pour les fonctions. Il écoute les déclencheurs et appelle les fonctions. Les étapes suivantes créent un hôte implémentant [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost), qui est l’hôte générique dans ASP.NET Core.

1. Sélectionnez l’onglet **Program.cs** et ajoutez les instructions `using` suivantes :

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Toujours sous **Program.cs**, remplacez la méthode `Main` par le code suivant :

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

Dans ASP.NET Core, les configurations de l’hôte sont définies par l’appel de méthodes sur l’instance [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Pour plus d’informations, consultez [Hôte générique .NET](/aspnet/core/fundamentals/host/generic-host). La méthode d’extension `ConfigureWebJobs` initialise l’hôte WebJobs. Dans `ConfigureWebJobs`, initialisez des extensions de liaison spécifiques, telles que l’extension de liaison Stockage, et définissez les propriétés de ces extensions.  

### <a name="enable-console-logging"></a>Activer la journalisation de console

Configurez la journalisation de console qui utilise le [framework de journalisation ASP.NET Core](/aspnet/core/fundamentals/logging). Ce framework, Microsoft.Extensions.Logging, comprend une API qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers.

1. Obtenez la version stable la plus récente du [package NuGet `Microsoft.Extensions.Logging.Console`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), qui inclut `Microsoft.Extensions.Logging`.

2. Dans la commande suivante, remplacez `<3_X_VERSION>` par le numéro de la version actuelle que vous avez trouvé à l’étape 1. Chaque type de package NuGet possède un numéro de version unique.

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
3. Dans la **console du gestionnaire de package**, indiquez le numéro de la version actuelle et exécutez la commande. La liste d’extensions s’affiche et s’installe automatiquement. 

4. Sous l’onglet **Program.cs**, ajoutez l’instruction `using` suivante :

   ```cs
   using Microsoft.Extensions.Logging;
   ```
5. En continuant sous **Program.cs**, ajoutez la méthode [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) à [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), avant la commande `Build`. La méthode [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) ajoute la journalisation de console à la configuration.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    La méthode `Main` se présente désormais ainsi :

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Cet ajout apporte les modifications suivantes :

    * Désactive la [journalisation du tableau de bord](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Le tableau de bord est un outil de surveillance hérité ; la journalisation du tableau de bord n’est pas recommandée pour les scénarios de production de débit élevé.
    * Ajoute le fournisseur de console avec le [filtrage](webjobs-sdk-how-to.md#log-filtering) par défaut.

Vous pouvez désormais ajouter une fonction qui est déclenchée par des messages arrivant dans une file d’attente du stockage Azure.

## <a name="add-a-function"></a>Ajouter une fonction

Une fonction est une unité de code qui s’exécute selon une planification, qui est déclenchée en fonction d’événements ou qui est exécutée à la demande. Un déclencheur écoute un événement de service. Dans le contexte du Kit de développement logiciel (SDK) WebJobs, le déclencheur ne fait pas référence au mode de déploiement. Les tâches web basées sur des événements ou planifiées qui sont créées à l’aide du Kit de développement logiciel (SDK) doivent toujours être déployées en tant que tâches web continues avec le paramètre Always-on activé. 

Dans cette section, vous créez une fonction déclenchée par des messages dans une file d’attente Stockage Azure. Tout d’abord, vous devez ajouter une extension de liaison pour vous connecter à Stockage Azure.

### <a name="install-the-storage-binding-extension"></a>Installer l’extension liaison de stockage

À partir de la version 3 du Kit de développement logiciel (SDK) WebJobs, pour vous connecter aux services Stockage Azure, vous devez installer un package distinct d’extension de liaison Stockage. 

1. Obtenez la dernière version stable du package NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), version 3.x.

1. Dans la commande suivante, remplacez `<3_X_VERSION>` par le numéro de la version actuelle que vous avez trouvé à l’étape 1. Chaque type de package NuGet possède un numéro de version unique. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
1. Dans la **console du gestionnaire de package**, exécutez la commande avec le numéro de la version actuelle au niveau du point d’entrée `PM>`.

1. En continuant dans **Program.cs**, dans la méthode d’extension `ConfigureWebJobs`, ajoutez la méthode `AddAzureStorage` sur l’instance [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) (avant la commande `Build`) pour initialiser l’extension Stockage. À ce stade, la méthode `ConfigureWebJobs` ressemble à ceci :

    ```cs
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    ```
1. Ajoutez le code suivant dans la méthode `Main` après l’instanciation de `builder` :

    ```csharp
    builder.UseEnvironment(EnvironmentName.Development);
     ```

    L’exécution en [mode de développement](webjobs-sdk-how-to.md#host-development-settings) réduit le [backoff exponentiel de l’interrogation de la file d’attente](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm) qui peut retarder considérablement le temps nécessaire au runtime pour trouver le message et appeler la fonction. Vous devez supprimer cette ligne de code ou basculer vers `Production` lorsque vous avez terminé le développement et les tests. 

    La méthode `Main` doit maintenant ressembler à l’exemple suivant :

    ```csharp
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
        {
            b.AddAzureStorageCoreServices();
        });
        builder.ConfigureLogging((context, b) =>
        {
            b.AddConsole();
        });
        builder.ConfigureWebJobs(b =>
        {
            b.AddAzureStorageCoreServices();
            b.AddAzureStorage();
        });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

### <a name="create-a-queue-triggered-function"></a>Créer une fonction déclenchée par une file d’attente

L’attribut `QueueTrigger` indique au runtime d’appeler cette fonction lorsqu’un nouveau message est écrit dans une file d’attente de stockage Azure appelée `queue`. Le contenu du message en file d’attente est fourni pour le code de méthode dans le paramètre `message`. Le corps de la méthode correspond à l’endroit où vous traitez les données du déclencheur. Dans cet exemple, le code ne fait qu’enregistrer le message.

1. Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** > **Nouvel élément**, puis sélectionnez **Classe**. 

2. Nommez le nouveau fichier de classe C# *Functions.cs*, puis sélectionnez **Ajouter**.

3. Dans *Functions.cs*, remplacez le modèle généré par le code suivant :
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

    Lorsqu’un message est ajouté à une file d’attente nommée `queue`, la fonction s’exécute et la chaîne `message` est écrite dans les journaux. La file d’attente analysée se trouve dans le compte Stockage Azure par défaut, que vous créez ensuite.
   
Le paramètre `message` ne doit pas nécessairement être une chaîne. Vous pouvez également le lier à un objet JSON, un tableau d’octets ou un objet [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage). Consultez la section relative à [l’utilisation des déclencheurs de file d’attente](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Chaque type de liaison (par exemple, des files d’attente, des objets blob ou des tables) dispose d’un ensemble différent de types de paramètre auquel vous pouvez lier des éléments.

### <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

L’émulateur de Stockage Azure exécuté localement ne possède pas toutes les fonctionnalités nécessaires au Kit de développement logiciel (SDK) WebJobs. Vous allez créer un compte de stockage dans Azure et configurer le projet pour qu’il l’utilise. 

Pour apprendre à créer un compte de stockage v2 universel, consultez [Créer un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal).

### <a name="locate-and-copy-your-connection-string"></a>Localiser et copier votre chaîne de connexion
Une chaîne de connexion est requise pour configurer le stockage. Conservez cette chaîne de connexion pour les étapes suivantes.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage et sélectionnez **Paramètres**.
1. Dans **Paramètres**, sélectionnez **Clés d’accès**.
1. Pour **Chaîne de connexion** sous **key1**, sélectionnez l’icône **Copier dans le Presse-papiers**.

     ![key](./media/webjobs-sdk-get-started/connection-key.png)

### <a name="configure-storage-to-run-locally"></a>Configurer le stockage pour une exécution locale

Le kit SDK WebJobs recherche la chaîne de connexion de stockage dans les Paramètres de l’application dans Azure. Si vous l’exécutez localement, il recherche cette valeur dans le fichier de configuration local ou dans les variables d’environnement.

1. Cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** > **Nouvel élément**, sélectionnez **Fichier de configuration JSON JavaScript**, nommez le nouveau fichier *appsettings.json*, puis sélectionnez **Ajouter**. 

1. Dans le nouveau fichier, ajoutez un champ `AzureWebJobsStorage`, comme dans l’exemple suivant :

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Remplacez *{storage connection string}* par la chaîne de connexion que vous avez copiée précédemment.

1. Sélectionnez le fichier *appsettings.json* dans Explorateur de solutions et, dans la fenêtre **Propriétés**, définissez l’action **Copier dans le répertoire de sortie** sur **Copier si plus récent**.

Comme ce fichier contient un secret de chaîne de connexion, vous ne devez pas le stocker dans un référentiel de code distant. Après avoir publié votre projet sur Azure, vous pouvez ajouter le même paramètre de chaîne de connexion dans votre application dans Azure App Service.

## <a name="test-locally"></a>Tester les topologies localement

Générez et exécutez le projet localement et créez une file d’attente de messages pour déclencher la fonction.

1. Dans **Cloud Explorer** de Visual Studio, développez le nœud correspondant au nouveau compte de stockage, puis cliquez avec le bouton droit sur **Files d’attente**.

1. Sélectionnez **Créer une file d’attente**.

1. Entrez *file d’attente* comme nom de la file d’attente, puis sélectionnez **OK**.

   ![Capture d’écran montrant où vous créez la file d’attente et la nommez « file d’attente ». ](./media/webjobs-sdk-get-started/create-queue.png)

1. Cliquez avec le bouton droit sur le nœud de la nouvelle file d’attente, puis sélectionnez **Ouvrir**.

1. Sélectionnez l’icône **Ajouter un message**.

   ![Capture d’écran mettant en évidence l’icône Ajouter un message.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Dans la boîte de dialogue **Ajouter un message**, entrez *Hello World!* en tant que **Texte du message**, puis sélectionnez **OK**. La file d'attente contient maintenant un message.

   ![Créer la file d’attente](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Appuyez sur **Ctrl+F5** pour exécuter le projet.

   La console indique que le runtime a trouvé votre fonction. Comme vous avez utilisé l’attribut `QueueTrigger` dans la fonction `ProcessQueueMessage`, le runtime WebJobs écoute les messages dans le file d’attente nommée `queue`. Lorsqu’il trouve un nouveau message dans cette file d’attente, le runtime appelle la fonction en lui transmettant la valeur de chaîne du message.

1. Revenez à la fenêtre **File d’attente** et actualisez-la. Le message n'y apparaît plus car il a été traité par votre fonction exécutée localement.

1. Fermez la fenêtre de console. 

Il est maintenant temps de publier votre projet de Kit de développement logiciel (SDK) WebJobs sur Azure.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Déployer sur Azure

Lors du déploiement, vous créez une instance de service d’application dans laquelle vous exécuterez vos fonctions. Lorsque vous publiez une application console .NET Core sur App Service dans Azure, celle-ci s’exécute automatiquement en tant que tâche web. Pour plus d'informations sur la publication, consultez [Développer et déployer des WebJobs à l’aide de Visual Studio](webjobs-dotnet-deploy-vs.md).

### <a name="create-azure-resources"></a>Créer des ressources Azure

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

### <a name="enable-always-on"></a>Activer AlwaysOn

Pour une tâche web continue, vous devez activer le paramètre Always-on sur le site pour que vos tâches web s’exécutent correctement. Si vous n’activez pas le paramètre Always-on, le runtime devient inactif après quelques minutes d’inactivité. 

1. Dans la page **Publier**, sélectionnez le bouton de sélection au-dessus d’**Hébergement** pour afficher les **actions de la section Profil d’hébergement**, puis choisissez **Ouvrir dans Portail Azure**.  

1. Sous **Paramètres**, choisissez **Configuration** > **Paramètres généraux**, définissez **Always-on** sur **Activé**, puis sélectionnez **Enregistrer** et **Continuer** pour redémarrer le site.

### <a name="publish-the-project"></a>Publication du projet

Une fois l’application web créée dans Azure, il est temps de publier le projet WebJobs. 

1. Dans la page **Publier**, sous **Hébergement**, sélectionnez le bouton de modification, modifiez le **type de tâche web** en `Continuous` et sélectionnez **Enregistrer**. Cela permet de s’assurer que le tâche web est en cours d’exécution lorsque des messages sont ajoutés à la file d’attente. Les tâches web déclenchées sont généralement utilisées uniquement pour les webhooks manuels. 

1. Sélectionnez le bouton **Publier** dans le coin supérieur droit de la page **Publier**. Une fois l’opération terminée, votre tâche web s’exécute sur Azure.

### <a name="create-a-storage-connection-app-setting"></a>Créer un paramètre d’application de connexion de stockage

Vous devez créer le même paramètre de chaîne de connexion de stockage dans Azure que celui que vous avez utilisé localement dans votre fichier config appsettings.json. Cela vous permet de stocker de manière plus sécurisée la chaîne de connexion.   

1. Dans votre page de profil **Publier**, sélectionnez le bouton de sélection au-dessus d’**Hébergement** pour afficher les **actions de la section Profil d’hébergement**, puis choisissez **Gérer les paramètres Azure App Service**.

1. Dans **Paramètres d’application**, choisissez **+ Ajouter un paramètre**.

1. Dans **Nom du nouveau paramètre d’application**, saisissez `AzureWebJobsStorage` et sélectionnez **OK**. 
 
1. Dans **Distant**, collez la chaîne de connexion de votre paramètre local, puis sélectionnez **OK**. 

La chaîne de connexion est maintenant définie dans votre application dans Azure.

### <a name="trigger-the-function-in-azure"></a>Déclencher la fonction dans Azure

1. Assurez-vous de ne pas exécuter en local. Fermez la fenêtre de la console si elle est toujours ouverte. Sinon, l’instance locale pourrait être la première à traiter les messages de la file d’attente que vous créez.

1. Dans la page **File d’attente** de Visual Studio, ajoutez un message à la file d’attente, comme vous l’avez déjà fait.

1. Actualisez la page **File d’attente** ; le nouveau message disparaît, car il a été traité par la fonction qui s’exécute dans Azure.

## <a name="enable-application-insights-logging"></a>Activer la journalisation dans Application Insights

Lorsque la tâche web s’exécute dans Azure, vous ne pouvez pas surveiller l’exécution de la fonction en affichant la sortie de la console. Pour pouvoir surveiller vos tâche web, vous devez créer une instance [Application Insights](../azure-monitor/app/app-insights-overview.md) associée lorsque vous publiez votre projet.

### <a name="create-an-application-insights-instance"></a>Créer une instance Application Insights

1. Dans votre page de profil **Publier**, sélectionnez le bouton de sélection au-dessus d’**Hébergement** pour afficher les **actions de la section Profil d’hébergement**, puis choisissez **Ouvrir dans Portail Azure**.

1. Dans l’application web sous **Paramètres**, choisissez **Application Insights**, puis sélectionnez **Activer Application Insights**.

1. Vérifiez le **nom de ressource** généré pour l’instance et l’**emplacement**, puis sélectionnez **Appliquer**. 

1. Sous **Paramètres**, choisissez **Configuration** et vérifiez qu’un nouveau `APPINSIGHTS_INSTRUMENTATIONKEY` a été créé. Cette clé est utilisée pour connecter votre instance WebJobs à Application Insights.   

Pour tirer parti de la journalisation d’[Application Insights](../azure-monitor/app/app-insights-overview.md), vous devez également mettre à jour votre code de journalisation.

### <a name="install-the-application-insights-extension"></a>Installer l’extension Application Insights

1. Obtenez la dernière version stable du package NuGet [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights), version 3.x.

2. Dans la commande suivante, remplacez `<3_X_VERSION>` par le numéro de la version actuelle que vous avez trouvé à l’étape 1. Chaque type de package NuGet possède un numéro de version unique. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
    ```
3. Dans la **console du gestionnaire de package**, exécutez la commande avec le numéro de la version actuelle au niveau du point d’entrée `PM>`.

### <a name="initialize-the-application-insights-logging-provider"></a>Initialiser le fournisseur de journalisation d’Application Insights

Ouvrez *Program.cs* et ajoutez l’initialiseur suivant dans le `ConfigureLogging` après l’appel à `AddConsole` :

```csharp
// If the key exists in settings, use it to enable Application Insights.
string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
if (!string.IsNullOrEmpty(instrumentationKey))
{
    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
}
```

Le code de méthode `Main` doit maintenant ressembler à l’exemple suivant : 

```csharp
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment(EnvironmentName.Development);
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    builder.ConfigureLogging((context, b) =>
            {
                b.AddConsole();

                // If the key exists in settings, use it to enable Application Insights.
                string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                if (!string.IsNullOrEmpty(instrumentationKey))
                {
                    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                }
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Ceci initialise le fournisseur de journalisation d’Application Insights avec un [filtrage](webjobs-sdk-how-to.md#log-filtering) par défaut. Lors de l’exécution locale, toutes les informations et les journaux de niveau supérieur sont écrits à la fois dans la console et dans Application Insights.

### <a name="republish-the-project-and-trigger-the-function-again"></a>Republier le projet et déclencher à nouveau la fonction

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

1. Comme ci-dessus, utilisez **Cloud Explorer** de Visual Studio pour créer un message de file d’attente, comme vous l’avez fait [précédemment](#test-locally), mais, cette fois-ci, entrez *Hello App Insights !* comme texte du message.

1. Dans votre page de profil **Publier**, sélectionnez le bouton de sélection au-dessus d’**Hébergement** pour afficher les **actions de la section Profil d’hébergement**, puis choisissez **Ouvrir dans Portail Azure**.

1. Dans l’application web sous **Paramètres**, choisissez **Application Insights** et sélectionnez **Afficher les données Application Insights**.

1. Sélectionnez **Rechercher**, puis sélectionnez **Voir toutes les données des dernières 24 heures**.

   ![Sélectionner Recherche](./media/webjobs-sdk-get-started/select-search.png)

1. Si le message *Hello App Insights !* ne s’affiche pas, sélectionnez **Actualiser** à plusieurs reprises pendant plusieurs minutes. Les journaux d’activité n’apparaissent pas immédiatement, car un certain temps est nécessaire au client Application Insights pour vider les journaux d’activité traités.

   ![Journaux d’activité dans Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

## <a name="add-inputoutput-bindings&quot;></a>Ajouter des liaisons d’entrée/sortie

Les liaisons simplifient le code qui lit et écrit les données. Les liaisons d’entrée simplifient le code qui lit les données. Les liaisons de sortie simplifient le code qui écrit les données.  

### <a name=&quot;add-input-binding&quot;></a>Ajouter une liaison d’entrée

Les liaisons d’entrée simplifient le code qui lit les données. Dans cet exemple, le message de file d’attente est le nom d’un blob, que vous utiliserez pour rechercher et lire un blob dans Stockage Azure.

1. Dans *Functions.cs*, remplacez la méthode `ProcessQueueMessage` par le code suivant :

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger(&quot;queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Dans ce code, `queueTrigger` est une [expression de liaison](../azure-functions/functions-bindings-expressions-patterns.md), ce qui signifie qu’elle est résolue en une valeur différente lors de l’exécution.  Lors de l’exécution, son contenu est celui du message en file d’attente.

1. Ajoutez un paramètre `using` :

   ```cs
   using System.IO;
   ```

1. Créez un conteneur d’objets blob dans votre compte de stockage.

   a. Dans **Cloud Explorer** de Visual Studio, développez le nœud de votre compte de stockage, cliquez avec le bouton droit sur **Blobs**, puis sélectionnez **Créer un conteneur de blobs**.

   b. Dans la boîte de dialogue **Créer un conteneur de blobs**, entrez *conteneur* comme nom du conteneur, puis sélectionnez **OK**.

1. Chargez le fichier *Program.cs* dans le conteneur d’objets blob. (Ce fichier est utilisé ici à titre d’exemple. Vous pouvez charger un fichier texte et créer un message en file d’attente portant le nom du fichier.)

   a. Dans **Cloud Explorer**, double-cliquez sur le nœud correspondant au conteneur que vous avez créé.

   b. Dans la fenêtre **Conteneur**, sélectionnez le bouton **Charger**.

   ![Bouton Charger l’objet blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Recherchez et sélectionnez *Program.cs*, puis sélectionnez **OK**.

1. Créez un message en file d’attente dans la file d’attente que vous avez créée précédemment, avec *Program.cs* comme texte du message.

   ![Message en file d’attente Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Exécutez le projet localement.

   Le message en file d’attente déclenche la fonction, qui lit l’objet blob et consigne sa longueur. La sortie de console se présente ainsi :

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```
### <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

Les liaisons de sortie simplifient le code qui écrit les données. Cet exemple modifie l’exemple précédent en écrivant une copie de l’objet blob au lieu de consigner sa taille. Les liaisons du stockage d’objets Blob sont incluses dans le package d’extension du stockage Azure que nous avons installé précédemment.

1. Remplacez la méthode `ProcessQueueMessage` par le code suivant :

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Créez un autre message en file d’attente avec *Program.cs* comme texte du message.

1. Exécutez le projet localement.

   Le message en file d’attente déclenche la fonction, qui lit l’objet blob, consigne sa longueur et crée un objet blob. La sortie de la console est identique, mais lorsque vous accédez à la fenêtre Conteneur d’objets blob et sélectionnez **Actualiser**, vous pouvez observer un nouvel objet blob nommé *copy-Program.cs*.

### <a name="republish-the-project"></a>Republier le projet

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

1. Dans la boîte de dialogue **Publier**, vérifiez que le profil actuel est sélectionné, puis sélectionnez **Publier**. Les résultats de la publication sont détaillés dans la fenêtre **Sortie**.
 
1. Vérifiez la fonction dans Azure en rechargeant un fichier dans le conteneur d'objets blob et en ajoutant un message à la file d’attente correspondant au nom du fichier téléchargé. Comme vous pourrez le constater, le message est supprimé de la file d’attente et une copie du fichier est créée dans le conteneur d’objets blob. 

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment créer, exécuter et déployer un projet de Kit de développement logiciel (SDK) WebJobs 3.x.

> [!div class="nextstepaction"]
> [En savoir plus sur le kit SDK WebJobs](webjobs-sdk-how-to.md)