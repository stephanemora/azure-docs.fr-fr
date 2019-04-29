---
title: Prise en main du Kit de développement logiciel (SDK) WebJobs (Azure)
description: Introduction au Kit de développement logiciel (SDK) WebJobs pour le traitement en arrière-plan basé sur les événements. Découvrez comment accéder aux données des services Azure et des services tiers.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: b824c99a015cfa2c1d1c75e2a1257eff482e8dd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60833201"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Prise en main du Kit de développement logiciel (SDK) Azure WebJobs pour le traitement en arrière-plan basé sur les événements

Cet article explique comment utiliser Visual Studio 2017 pour créer un projet de kit de développement logiciel Azure WebJobs, exécutez-le localement et déployez-le dans [Azure App Service](overview.md). Le projet que vous créez est une application de console .NET Core, qui utilise la version 3.x du SDK WebJobs. Si vous êtes intéressé par version 2.x, qui utilise le .NET Framework, consultez [développer et déployer des tâches Web à l’aide de Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

Pour en savoir plus sur l’utilisation avec le SDK WebJobs, consultez [comment utiliser le SDK Azure WebJobs pour le traitement d’arrière-plan pilotée par événements](webjobs-sdk-how-to.md).

## <a name="prerequisites"></a>Conditions préalables

* [Installez Visual Studio 2017](/visualstudio/install/) avec la charge de travail de **développement Azure**. Si vous possédez déjà Visual Studio, mais que vous n’avez pas cette charge de travail, ajoutez-la en sélectionnant **Outils > Get Tools and Features** (Obtenir des outils et des fonctionnalités).

* Vous devez avoir [un compte Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) pour publier votre projet de SDK WebJobs sur Azure.

## <a name="create-a-project"></a>Création d’un projet

1. Dans Visual Studio, sélectionnez **Fichier > Nouveau > Projet**.

2. Sélectionnez **.NET Core > application (.NET Core) de Console**.

3. Nommez le projet *WebJobsSDKSample*, puis sélectionnez **OK**.

   ![Boîte de dialogue Nouveau projet](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Packages NuGet WebJobs

1. Installez les dernières versions 3.x stables des packages NuGet suivants :

   * `Microsoft.Azure.WebJobs`
   * `Microsoft.Azure.WebJobs.Extensions`

     Voici le **Console du Gestionnaire de Package** commandes pour la version 3.0.4 :

     ```powershell
     Install-Package Microsoft.Azure.WebJobs -version 3.0.4
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
     ```

## <a name="create-the-host"></a>Créer l’hôte

L’hôte est le conteneur d’exécution pour les fonctions qui écoute les déclencheurs et appelle des fonctions. Les étapes suivantes créent un hôte qui implémente [ `IHost` ](/dotnet/api/microsoft.extensions.hosting.ihost), qui est l’hôte générique dans ASP.NET Core.

1. Dans *Program.cs*, ajoutez une instruction `using` :

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Remplacez la méthode `Main` par le code suivant :

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

Dans ASP.NET Core, les configurations de l’hôte sont définies par l’appel de méthodes sur l’instance [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Pour plus d’informations, consultez [Hôte générique .NET](/aspnet/core/fundamentals/host/generic-host). La méthode d’extension `ConfigureWebJobs` initialise l’hôte WebJobs. Dans `ConfigureWebJobs`, initialiser les extensions spécifiques de tâches Web et de définir les propriétés de ces extensions.  

## <a name="enable-console-logging"></a>Activer la journalisation de console

Dans cette section, vous configurez la journalisation de console qui utilise le [framework de journalisation ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Installez la dernière version stable des packages NuGet suivants :

   * `Microsoft.Extensions.Logging` : framework de journalisation.
   * `Microsoft.Extensions.Logging.Console` -Le fournisseur console, qui envoie des journaux à la console.

   Voici les commandes **Console du Gestionnaire de package** pour la version 2.2.0 :

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. Dans *Program.cs*, ajoutez une instruction `using` :

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Appelez la méthode [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) sur [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). La méthode [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) ajoute la journalisation de console à la configuration.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    La méthode `Main` se présente désormais ainsi :

    ```cs
    static void Main(string[] args)
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
            host.Run();
        }
    }
    ```

    Cette mise à jour effectue les opérations suivantes :

    * Désactive la [journalisation du tableau de bord](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Le tableau de bord est un outil de surveillance hérité ; la journalisation du tableau de bord n’est pas recommandée pour les scénarios de production de débit élevé.
    * Ajoute le fournisseur de console avec le [filtrage](webjobs-sdk-how-to.md#log-filtering) par défaut.

Vous pouvez désormais ajouter une fonction qui est déclenchée par des messages arrivant dans une [file d’attente du stockage Azure](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Installer l’extension liaison de stockage

Depuis la version 3.x, vous devez installer explicitement l’extension de liaison de stockage requise par le SDK WebJobs. Dans les versions antérieures, les liaisons de stockage ont été inclus dans le Kit de développement.

1. Installez la dernière version stable du package NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), version 3.x. 

    Voici le **Console du Gestionnaire de Package** commande pour la version 3.0.3 :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.3
    ```

2. Dans la méthode d’extension `ConfigureWebJobs`, appelez la méthode `AddAzureStorage` sur l’instance [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) pour initialiser l’extension de stockage. À ce stade, la méthode `ConfigureWebJobs` est semblable à l’exemple suivant :

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Créer une fonction

1. Cliquez sur le projet, sélectionnez **ajouter** > **un nouvel élément...** , choisissez **classe**, nommez le nouveau C# fichier de classe *Functions.cs*, puis sélectionnez **ajouter**.

1. Dans Functions.cs, remplacez le modèle généré par le code suivant :

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

   L’attribut `QueueTrigger` indique au runtime d’appeler cette fonction lorsqu’un nouveau message est écrit dans une file d’attente de stockage Azure appelée `queue`. Le contenu du message en file d’attente est fourni pour le code de méthode dans le paramètre `message`. Le corps de la méthode correspond à l’endroit où vous traitez les données du déclencheur. Dans cet exemple, le code ne fait qu’enregistrer le message.

   Le paramètre `message` ne doit pas nécessairement être une chaîne. Vous pouvez également le lier à un objet JSON, un tableau d’octets ou un objet [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage). Consultez la section relative à [l’utilisation des déclencheurs de file d’attente](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Chaque type de liaison (par exemple, des files d’attente, des objets blob ou des tables) dispose d’un ensemble différent de types de paramètre auquel vous pouvez lier des éléments.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

L’émulateur de stockage Azure exécuté localement ne possède pas toutes les fonctionnalités nécessaires au Kit de développement logiciel (SDK) WebJobs. Par conséquent, dans cette section vous créez un compte de stockage dans Azure et configurez le projet pour l’utiliser. Si vous avez déjà un compte de stockage, passez à l’étape 6.

1. Ouvrez **l’Explorateur de serveurs** dans Visual Studio et connectez-vous à Azure. Cliquez avec le bouton droit sur le nœud **Azure**, puis sélectionner **Se connecter à un abonnement Microsoft Azure**.

   ![Connexion à Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Sous le nœud **Azure** de **l’Explorateur de serveurs**, cliquez avec le bouton droit sur **Stockage**, puis sélectionnez **Créer un compte de stockage**.

   ![Menu Créer un compte de stockage](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Dans la boîte de dialogue **Créer un compte de stockage**, entrez un nom unique pour le compte de stockage.

1. Choisissez la **région** dans laquelle vous avez créé votre application App Service ou une région qui se trouve à proximité.

1. Sélectionnez **Créer**.

   ![Créer un compte de stockage](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Sous le nœud **Stockage** dans **l’Explorateur de serveurs**, sélectionnez le nouveau compte de stockage. Dans la fenêtre **Propriétés**, sélectionnez les points de suspension (**...** ) à droite du champ de valeur **Chaîne de connexion**.

   ![Points de suspension du champ Chaîne de connexion](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copiez la chaîne de connexion et enregistrez cette valeur là où vous pouvez la copier à nouveau facilement.

   ![Copier la chaîne de connexion](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Configurer le stockage pour une exécution locale

Le kit SDK WebJobs recherche la chaîne de connexion de stockage dans les Paramètres de l’application dans Azure. Si vous l’exécutez localement, il recherche cette valeur dans le fichier de configuration local ou dans les variables d’environnement.

1. Cliquez sur le projet, sélectionnez **ajouter** > **un nouvel élément...** , choisissez **fichier config JSON JavaScript**, nommez le nouveau fichier *appsettings.json* et sélectionnez **ajouter**. 

1. Dans le nouveau fichier, ajoutez un `AzureWebJobsStorage` champ, comme dans l’exemple suivant :

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Remplacez *{chaîne de connexion de stockage}* par la chaîne de connexion que vous avez copiée précédemment.

1. Sélectionnez le *appsettings.json* fichier dans l’Explorateur de solutions et, dans le **propriétés** fenêtre, définissez **Copy to Output Directory** à **Copier si plus récent**.

Plus tard, vous allez ajouter le même paramètre d’application de chaîne de connexion à votre application dans Azure App Service.

## <a name="test-locally"></a>Tester les topologies localement

Dans cette section, vous allez générer et exécuter le projet localement et déclencher la fonction en créant un message en file d’attente.

1. Appuyez sur **Ctrl + F5** pour exécuter le projet.

   La console indique que le runtime a trouvé votre fonction et attend que les messages en file d’attente le déclenchent. La sortie suivante est générée par l’hôte v3.x :

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Fermez la fenêtre de console.

1. Dans **l’Explorateur de serveurs** de Visual Studio, développez le nœud correspondant au nouveau compte de stockage, puis cliquez avec le bouton droit sur **Files d’attente**.

1. Sélectionnez **Créer une file d’attente**.

1. Entrez *file d’attente* comme nom de la file d’attente, puis sélectionnez **OK**.

   ![Créer la file d’attente](./media/webjobs-sdk-get-started/create-queue.png)

1. Cliquez avec le bouton droit sur le nœud de la nouvelle file d’attente, puis sélectionnez **Afficher la file d’attente**.

1. Sélectionnez l’icône **Ajouter un message**.

   ![Créer la file d’attente](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Dans la boîte de dialogue **Ajouter un message**, entrez *Hello World!* en tant que **Texte du message**, puis sélectionnez **OK**. Il existe désormais un message dans la file d’attente.

   ![Créer la file d’attente](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Réexécutez le projet.

   Comme vous avez utilisé l’attribut `QueueTrigger` dans la fonction `ProcessQueueMessage`, le runtime du Kit de développement logiciel (SDK) WeJobs écoute les messages en file d’attente lors du démarrage. Il recherche un nouveau message dans la file d’attente nommée *file d’attente* et appelle la fonction.

   En raison de la [temporisation exponentielle de l’interrogation de la file d’attente](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), 2 minutes peuvent être nécessaires au runtime pour trouver le message et appeler la fonction. Ce délai d’attente peut être réduit en lançant l’exécution en [mode de développement](webjobs-sdk-how-to.md#host-development-settings).

   La sortie de console se présente ainsi :

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Fermez la fenêtre de console. 

1. Revenez à la fenêtre de la file d’attente et l’actualiser. Le message a disparu, dans la mesure où il a été traité par votre fonction en cours d’exécution localement. 

## <a name="add-application-insights-logging"></a>Ajouter la journalisation dans Application Insights

Lorsque le projet s’exécute dans Azure, vous ne pouvez pas surveiller l’exécution de la fonction en affichant la sortie de la console. Nous vous recommandons d’utiliser [Application Insights](../azure-monitor/app/app-insights-overview.md) comme solution de supervision. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](../azure-functions/functions-monitoring.md).

Dans cette section, vous allez effectuer les tâches suivantes pour configurer la journalisation dans Application Insights avant de procéder au déploiement sur Azure :

* Vérifiez que vous disposez d’une application App Service et d’une instance Application Insights à utiliser.
* Configurez l’application App Service pour utiliser l’instance Application Insights et le compte de stockage que vous avez créés précédemment.
* Configurez le projet pour la journalisation dans Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Créer une application App Service et une instance Application Insights

1. Si vous ne disposez pas déjà d’une application App Service que vous pouvez utiliser, [créez-en une](app-service-web-get-started-dotnet-framework.md). Lorsque vous créez votre application, vous pouvez également créer une ressource Application Insights connectée. Lorsque vous faites cela, `APPINSIGHTS_INSTRUMENTATIONKEY` est défini pour vous dans votre application.

1. Si vous ne disposez pas déjà d’une ressource Application Insights que vous pouvez utiliser, [créez-en une](../azure-monitor/app/create-new-resource.md ). Définissez l’option **Type d’application** sur **Général** et ignorez les sections qui suivent **Copier la clé d’instrumentation**.

1. Si vous disposez déjà d’une ressource Application Insights que vous souhaitez utiliser, [copiez la clé d’instrumentation](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Configuration des paramètres d’application 

1. Dans **l’Explorateur de serveurs** de Visual Studio, développez le nœud **App Service** sous **Azure**.

1. Développez le groupe de ressources dans lequel figure votre application App Service, puis cliquez avec le bouton droit sur votre application App Service.

1. Sélectionnez **Afficher les paramètres**.

1. Dans la zone **Chaînes de connexion**, ajoutez l’entrée suivante.

   |Nom  |Chaîne de connexion  |Type de base de données|
   |---------|---------|------|
   |AzureWebJobsStorage | {chaîne de connexion de stockage que vous avez copiée précédemment}|Personnalisée|

1. Si la zone **Paramètres de l’application** est dépourvue de clé d’instrumentation Application Insights, ajoutez celle que vous avez copiée précédemment. (La clé d’instrumentation figure peut-être déjà dans la zone selon la façon dont vous avez créé l’application App Service.)

   |Nom  |Valeur  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {clé d’instrumentation} |

1. Remplacez *{clé d’instrumentation}* par la clé d’instrumentation de la ressource Application Insights que vous utilisez.

1. Sélectionnez **Enregistrer**.

1. Ajouter la connexion d’Application Insights au projet afin que vous puissiez l’exécuter localement. Dans le fichier *appsettings.json*, ajoutez un champ `APPINSIGHTS_INSTRUMENTATIONKEY`, comme dans l’exemple suivant :

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Remplacez *{clé d’instrumentation}* par la clé d’instrumentation de la ressource Application Insights que vous utilisez.

1. Enregistrez vos modifications.

### <a name="add-application-insights-logging-provider"></a>Ajouter le fournisseur de journalisation Application Insights

Pour tirer parti de la journalisation [Application Insights](../azure-monitor/app/app-insights-overview.md), mettez à jour votre code de journalisation pour effectuer les opérations suivantes :

* Ajouter un fournisseur de journalisation Application Insights avec le [filtrage](webjobs-sdk-how-to.md#log-filtering) par défaut. Tous les journaux d’activité d’information et de niveau supérieur accèdent à la console et à Application Insights lorsque vous l’exécutez localement.
* Placer l’objet `LoggerFactory` dans un bloc `using` pour garantir le vidage de la sortie du journal lorsque l’hôte s’arrête.

1. Installez la dernière version 3.x stable du package NuGet pour le fournisseur de journalisation Application Insights : `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Voici la commande **Console du Gestionnaire de package** pour la version 3.0.2 :

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Ouvrez *Program.cs* et remplacez le code existant dans la méthode `Main` par le code suivant :

    ```cs
    static void Main(string[] args)
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
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Cela ajoute le fournisseur d’Application Insights pour la journalisation à l’aide de la clé que vous avez ajouté précédemment à vos paramètres d’application.

## <a name="test-application-insights-logging"></a>Tester la journalisation dans Application Insights

Dans cette section, vous allez effectuer une nouvelle exécution locale pour vérifier que les données de journalisation accèdent maintenant à Application Insights et à la console.

1. Utilisez **l’Explorateur de serveurs** de Visual Studio pour créer un message en file d’attente, comme vous l’avez fait [précédemment](#trigger-the-function-in-azure), mais cette fois-ci entrez *Hello App Insights !* comme texte du message.

1. Exécutez le projet.

   Le Kit de développement logiciel (SDK) WebJobs traite le message en file d’attente, et vous pouvez observer les journaux d’activité dans la fenêtre de console.

1. Fermez la fenêtre de console.

1. Ouvrez le [portail Azure](https://portal.azure.com/), puis accédez à la ressource Application Insights.

1. Sélectionnez **Recherche**.

   ![Sélectionner Recherche](./media/webjobs-sdk-get-started/select-search.png)

1. Si le message *Hello App Insights !* ne s’affiche pas, sélectionnez **Actualiser** à plusieurs reprises pendant plusieurs minutes. (Les journaux d’activité n’apparaissent pas immédiatement, car un certain temps est nécessaire au client Application Insights pour vider les journaux d’activité traités.)

   ![Journaux d’activité dans Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Fermez la fenêtre de console.

## <a name="deploy-as-a-webjob"></a>Déployer vers Azure

Au cours du déploiement, vous créez une instance de service d’application dans lequel exécuter vos fonctions. Lorsque vous publiez une application console .NET Core sur App Service dans Azure, il obtient automatiquement exécuté comme une tâche Web. Pour en savoir plus sur la publication, consultez [développer et déployer des tâches Web à l’aide de Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Déclencher la fonction dans Azure

1. Assurez-vous que l’exécution n’est pas locale (fermez la fenêtre de console si elle est toujours ouverte). Dans le cas contraire, l’instance locale risque d’être la première à traiter les messages en file d’attente que vous créez.

1. Dans la page **File d’attente** de Visual Studio, ajoutez un message à la file d’attente, comme vous l’avez déjà fait.

1. Actualisez la page **File d’attente** ; le nouveau message disparaît, car il a été traité par la fonction qui s’exécute dans Azure.

   > [!TIP]
   > Si vous procédez au test dans Azure, utilisez le [mode de développement](webjobs-sdk-how-to.md#host-development-settings) pour vous assurer qu’une fonction de déclenchement de file d’attente est appelée immédiatement et éviter les retards dus à la [temporisation exponentielle de l’interrogation de la file d’attente](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Afficher les journaux d’activité dans Application Insights

1. Ouvrez le [portail Azure](https://portal.azure.com/), puis accédez à la ressource Application Insights.

1. Sélectionnez **Recherche**.

1. Si le message *Hello Azure !* ne s’affiche pas, sélectionnez **Actualiser** à plusieurs reprises pendant plusieurs minutes.

   Vous pouvez observer les journaux d’activité de la fonction exécutée dans une tâche web, y compris le texte *Hello Azure !* que vous avez entré dans la section précédente.

## <a name="add-an-input-binding"></a>Ajouter une liaison d’entrée

Les liaisons d’entrée simplifient le code qui lit les données. Pour cet exemple, le message en file d’attente est un nom d’objet blob que vous allez utiliser pour rechercher et lire un objet blob dans Stockage Azure.

1. Dans *Functions.cs*, remplacez la méthode `ProcessQueueMessage` par le code suivant :

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
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

   a. Dans **l’Explorateur de serveurs** de Visual Studio, développez le nœud de votre compte de stockage, cliquez avec le bouton droit sur **Objets blob**, puis sélectionnez **Créer un conteneur d’objets blob**.

   b. Dans la boîte de dialogue **Créer un conteneur d’objets blob**, entrez *conteneur* comme nom du conteneur, puis cliquez sur **OK**.

1. Chargez le fichier *Program.cs* dans le conteneur d’objets blob. (Ce fichier est utilisé ici à titre d’exemple. Vous pouvez charger un fichier texte et créer un message en file d’attente portant le nom du fichier.)

   a. Dans **l’Explorateur de serveurs**, double-cliquez sur le nœud correspondant au conteneur que vous avez créé.

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

## <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

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

## <a name="republish-the-updates-to-azure"></a>Republier les mises à jour vers Azure

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

1. Dans le **publier** boîte de dialogue, assurez-vous que le profil actuel est sélectionné, puis choisissez **publier**. Résultats de la publication sont détaillées dans le **sortie** fenêtre.
 
1. Vérifier la fonction dans Azure en chargeant un fichier sur le conteneur d’objets blob à nouveau et en ajoutant un message à la file d’attente qui est le nom du fichier téléchargé. Vous voyez le message d’être supprimées de la file d’attente et une copie du fichier créé dans le conteneur d’objets blob. 

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment créer, exécuter et déployer un projet de 3.x SDK WebJobs.

> [!div class="nextstepaction"]
> [En savoir plus sur le kit SDK WebJobs](webjobs-sdk-how-to.md)
