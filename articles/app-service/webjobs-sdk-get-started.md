---
title: Prise en main du Kit de développement logiciel (SDK) WebJobs (Azure)
description: Introduction au Kit de développement logiciel (SDK) WebJobs pour le traitement en arrière-plan basé sur les événements. Découvrez comment accéder aux données des services Azure et des services tiers.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/15/2019
ms.author: glenga
ms.openlocfilehash: b39c8c19b494422d34bae7145d69e8e95cb16846
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416818"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Prise en main du Kit de développement logiciel (SDK) Azure WebJobs pour le traitement en arrière-plan basé sur les événements

Cet article explique comment créer un projet de Kit de développement logiciel (SDK) Azure WebJobs, l’exécuter localement et le déployer vers Azure App Service.

Les instructions concernent [Visual Studio 2017](https://www.visualstudio.com/vs/), mais les mêmes tâches peuvent être accomplies avec d’autres outils, tels que [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Présentation du Kit de développement logiciel (SDK) Azure WebJobs

Le Kit de développement logiciel (SDK) Azure WebJobs est une infrastructure qui simplifie l’écriture d’un code de traitement en arrière-plan permettant d’accéder aux données des services Azure. Le Kit de développement logiciel propose une syntaxe déclarative permettant de spécifier les événements qui doivent déclencher une fonction, comme un nouveau message ajouté à une file d’attente. Une syntaxe déclarative du même type contrôle la lecture et l’écriture des données une fois qu’une fonction a été déclenchée. Ce système de déclencheurs et de liaisons prend en charge la plupart des tâches de codage de niveau inférieur associées à l’accès aux services Azure et tiers.

### <a name="functions-triggers-and-bindings"></a>Fonctions, déclencheurs et liaisons

Un projet de Kit de développement logiciel (SDK) WebJobs définit une ou plusieurs *fonctions*. Une fonction est une méthode dont la signature contient un attribut déclencheur. Les déclencheurs spécifient des conditions pour appeler une fonction, tandis que les liaisons spécifient le contenu à lire et à écrire. Par exemple, l’attribut déclencheur de la fonction suivante indique au runtime d’appeler la fonction chaque fois qu’un message de la file d’attente s’affiche dans la file d’attente `items`. L’attribut `Blob` indique au runtime d’utiliser le message de la file d’attente pour lire un objet blob dans le conteneur *workitems*. Le contenu du message de la file d’attente &mdash; fourni dans le paramètre `queueTrigger` &mdash; est le nom de l’objet blob.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Versions 2.x et 3.x

Les instructions détaillent la création d’un projet de kit SDK WebJobs version 3.x. Le principal changement introduit par la version 3.x est l’utilisation de .NET Core au lieu de .NET Framework. Les différences entre la version 2.x et la version 3.x sont répertoriées dans cet article.

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) fournit une option serverless pour vos fonctions. S’appuyant sur le kit SDK WebJobs, Functions constitue une solution lorsque vous n’avez pas besoin d’utiliser directement le kit SDK WebJobs. Azure Functions 2.x utilise le kit SDK WebJobs 3.x. Pour plus d’informations, consultez la section relative à la [comparaison entre Azure Functions et le Kit de développement logiciel (SDK) WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Prérequis

Dans cet article, il est supposé que vous avez [un compte Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) et une expérience en matière [d’applications dans Azure App Service](overview.md). Pour accomplir les étapes décrites dans cet article :

* [Installez Visual Studio 2017](/visualstudio/install/) avec la charge de travail de **développement Azure**. Si vous possédez déjà Visual Studio, mais que vous n’avez pas cette charge de travail, ajoutez-la en sélectionnant **Outils > Get Tools and Features** (Obtenir des outils et des fonctionnalités).
* [Créez une application App Service](app-service-web-get-started-dotnet-framework.md). Si vous en avez déjà une sur laquelle vous pouvez déployer une tâche web, vous pouvez l’utiliser au lieu d’en créer une.

## <a name="create-a-project"></a>Création d’un projet

1. Dans Visual Studio, sélectionnez **Fichier > Nouveau > Projet**.

2. Sélectionnez **Bureau classique Windows > Application console (.NET Framework)**.

3. Nommez le projet *WebJobsSDKSample*, puis sélectionnez **OK**.

   ![Boîte de dialogue Nouveau projet](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Packages NuGet WebJobs

Les packages NuGet qui installent le kit SDK WebJobs diffère légèrement entre la version 2.x et la version 3.x.

### <a name="install-sdk-version-3x-packages"></a>Installer les packages du kit SDK version 3.x

1. Installez les dernières versions 3.x stables des packages NuGet suivants :

    * `Microsoft.Azure.WebJobs`
    * `Microsoft.Azure.WebJobs.Extensions`

    Voici les commandes **Console du Gestionnaire de package** pour la version 3.0.3 :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 3.0.2
    Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
    ```

### <a name="install-the-sdk-version-2x-package"></a>Installer le package du kit SDK version 2.x

1. Installez la dernière version 2.x stable du package NuGet `Microsoft.Azure.WebJobs`.

    Voici la commande **Console du Gestionnaire de package** pour la version 2.2.0 :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 2.2.0
    ```

## <a name="create-the-host"></a>Créer l’hôte

L’hôte est le conteneur d’exécution pour les fonctions : il écoute les déclencheurs et appelle les fonctions. L’hôte que vous créez dépend de la version du kit SDK.

### <a name="version-3x"></a>Version 3.x

Le code suivant crée un hôte implémentant [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost), qui est l’hôte générique dans ASP.NET Core. 

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

Dans ASP.NET Core, les configurations de l’hôte sont définies par l’appel de méthodes sur l’instance [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Pour plus d’informations, consultez [Hôte générique .NET](/aspnet/core/fundamentals/host/generic-host). La méthode d’extension `ConfigureWebJobs` initialise l’hôte WebJobs.

### <a name="version-2x"></a>Version 2.x

Le code suivant crée l’objet **JobHost**.

1. Dans *Program.cs*, ajoutez une instruction `using` :

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Remplacez la méthode `Main` par le code suivant :

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

Les configurations de l’hôte sont définies dans l’instance `JobHostConfiguration` utilisée pour créer `JobHost`.

## <a name="enable-console-logging"></a>Activer la journalisation de console

Pour les versions 2.x et 3.x du kit SDK WebJobs, nous recommandons le framework de journalisation qui a été développé pour ASP.NET Core. Cette infrastructure offre de meilleures performances et une plus grande souplesse dans le support de stockage et le filtrage. Pour plus d’informations, consultez [Journalisation dans ASP.NET Core](/aspnet/core/fundamentals/logging).

Dans cette section, vous allez configurer la journalisation de console qui utilise ce framework.

1. Installez la dernière version stable des packages NuGet suivants :

   * `Microsoft.Extensions.Logging` : framework de journalisation.
   * `Microsoft.Extensions.Logging.Console` : *fournisseur* de console. Un fournisseur envoie des journaux à une destination particulière, dans ce cas à la console.

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

1. Les mises à jour du code de journalisation varient selon votre version du kit SDK :

    **Version 3.x**

    Appelez la méthode [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) sur [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). La méthode [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) ajoute la journalisation de console à la configuration.

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

    **Version 2.x**

    Dans la méthode `Main`, ajoutez le code pour mettre à jour l’objet `JobHostConfiguration` avant de créer l’objet `JobHost` :

    ```cs
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    ```

    La méthode `Main` se présente désormais ainsi :

    ```cs
    var config = new JobHostConfiguration();
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    var host = new JobHost(config);
    host.RunAndBlock();
    ```

    Ces mises à jour effectuent les opérations suivantes :

    * Désactive la [journalisation du tableau de bord](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Le tableau de bord est un outil de surveillance hérité ; la journalisation du tableau de bord n’est pas recommandée pour les scénarios de production de débit élevé.
    * Ajoute le fournisseur de console avec le [filtrage](webjobs-sdk-how-to.md#log-filtering) par défaut.

Vous pouvez désormais ajouter une fonction qui est déclenchée par des messages arrivant dans une [file d’attente du stockage Azure](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-binding-extensions"></a>Installer des extensions de liaison

Depuis la version 3.x, vous devez installer explicitement les extensions des attributs de liaison du kit SDK WebJobs, attributs que vous utilisez dans vos fonctions. Les seules exceptions sont le [déclencheur de minuteur](../azure-functions/functions-bindings-timer.md) et le [déclencheur HTTP](../azure-functions/functions-bindings-http-webhook.md), qui n’ont pas besoin d’extension. La version 2.x du kit SDK WebJobs n’utilise pas d’extension, celles-ci sont incluses dans le kit SDK. Si vous utilisez la version 2.x, passez directement à la section suivante.

1. Installez la dernière version stable du package NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), version 3.x. 

    Voici la commande **Console du Gestionnaire de package** pour la version 3.0.2 :

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.2
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

1. Cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** > **Nouvel élément...**  et nommez le nouveau fichier de classe C# *Functions.cs*.

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

L’émulateur de stockage Azure exécuté localement ne possède pas toutes les fonctionnalités nécessaires au Kit de développement logiciel (SDK) WebJobs. Dans cette section, vous allez donc créer un compte de stockage dans Azure et configurer le projet pour l’utiliser.

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

### <a name="appsettingsjson-sdk-version-3x"></a>appSettings.json (kit SDK version 3.x)

1. Créez un fichier *appsettings.json*, ou ajoutez un champ `AzureWebJobsStorage`, comme dans l’exemple suivant :

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Remplacez *{chaîne de connexion de stockage}* par la chaîne de connexion que vous avez copiée précédemment.

### <a name="appconfig-sdk-version-2x"></a>App.config (kit SDK version 2.x)

1. Ajoutez le code XML suivant au fichier *App.config* immédiatement après la balise de début de `<configuration>`.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Remplacez *{chaîne de connexion de stockage}* par la chaîne de connexion que vous avez copiée précédemment.

Plus tard, vous allez ajouter le même paramètre d’application de chaîne de connexion à votre application dans Azure App Service.

## <a name="test-locally"></a>Tester les topologies localement

Dans cette section, vous allez générer et exécuter le projet localement et déclencher la fonction en créant un message en file d’attente.

1. Appuyez sur Ctrl+F5 pour exécuter le projet.

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

1. Dans la boîte de dialogue **Ajouter un message**, entrez *Hello World!* en tant que **Texte du message**, puis sélectionnez **OK**.

   ![Créer la file d’attente](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Réexécutez le projet.

   Comme vous avez utilisé l’attribut `QueueTrigger` dans la fonction `ProcessQueueMessage`, le runtime du Kit de développement logiciel (SDK) WeJobs écoute les messages en file d’attente lors du démarrage. Il recherche un nouveau message dans la file d’attente nommée *file d’attente* et appelle la fonction.

   En raison de la [temporisation exponentielle de l’interrogation de la file d’attente](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), 2 minutes peuvent être nécessaires au runtime pour trouver le message et appeler la fonction. Ce délai d’attente peut être réduit en lançant l’exécution en [mode de développement](webjobs-sdk-how-to.md#host-development-settings).

  Pour la version 3.x, la sortie de console se présente ainsi :

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

## <a name="add-application-insights-logging"></a>Ajouter la journalisation dans Application Insights

Lorsque le projet s’exécute dans Azure, vous ne pouvez pas surveiller l’exécution de la fonction en affichant la sortie de la console. Nous vous recommandons d’utiliser [Application Insights](../azure-monitor/app/app-insights-overview.md) comme solution de surveillance. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](../azure-functions/functions-monitoring.md).

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

1. Ajoutez la connexion Application Insights au projet, afin de pouvoir l’exécuter localement :

    **Version 3.x**

    Dans le fichier *appsettings.json*, ajoutez un champ `APPINSIGHTS_INSTRUMENTATIONKEY`, comme dans l’exemple suivant :

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    **Version 2.x**

    Ajoutez le code XML suivant au fichier *App.config* immédiatement après la collection des chaînes de connexion.

    ```xml
    <appSettings>
        <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
    </appSettings>
    ```

    Dans chaque version, remplacez *{clé d’instrumentation}* par la clé d’instrumentation provenant de la ressource Application Insights que vous utilisez.

1. Enregistrez vos modifications.

### <a name="add-application-insights-logging-provider"></a>Ajouter le fournisseur de journalisation Application Insights

Pour tirer parti de la journalisation [Application Insights](../azure-monitor/app/app-insights-overview.md), mettez à jour votre code de journalisation pour effectuer les opérations suivantes :

* Ajouter un fournisseur de journalisation Application Insights avec le [filtrage](webjobs-sdk-how-to.md#log-filtering) par défaut. Tous les journaux d’information et de niveau supérieur accèdent à la console et à Application Insights lorsque vous l’exécutez localement.
* Placer l’objet `LoggerFactory` dans un bloc `using` pour garantir le vidage de la sortie du journal lorsque l’hôte s’arrête.

#### <a name="version-3x"></a>Version 3.x

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

#### <a name="version-2x"></a>Version 2.x

1. Installez la dernière version 2.x stable du package NuGet pour le fournisseur de journalisation Application Insights : `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Voici la commande **Console du Gestionnaire de package** pour la version 2.2.0 :

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Installez la dernière version 4.x stable du package NuGet pour le Gestionnaire de configuration .NET : `System.Configuration.ConfigurationManager`.

   Voici la commande **Console du Gestionnaire de package** pour la version 4.4.1 :

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Ouvrez *Program.cs* et ajoutez une instruction `using` pour le Gestionnaire de configuration :

   ```csharp
   using System.Configuration;
   ```

1. Remplacez le code de la méthode `Main` par le code suivant :

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="test-application-insights-logging"></a>Tester la journalisation dans Application Insights

Dans cette section, vous allez effectuer une nouvelle exécution locale pour vérifier que les données de journalisation accèdent maintenant à Application Insights et à la console.

1. Utilisez **l’Explorateur de serveurs** de Visual Studio pour créer un message en file d’attente, comme vous l’avez fait [précédemment](#trigger-the-function-in-azure), mais cette fois-ci entrez *Hello App Insights !* comme texte du message.

1. Exécutez le projet.

   Le Kit de développement logiciel (SDK) WebJobs traite le message en file d’attente, et vous pouvez observer les journaux dans la fenêtre de console.

1. Fermez la fenêtre de console.

1. Ouvrez le [portail Azure](https://portal.azure.com/), puis accédez à la ressource Application Insights.

1. Sélectionnez **Recherche**.

   ![Sélectionner Recherche](./media/webjobs-sdk-get-started/select-search.png)

1. Si le message *Hello App Insights !* ne s’affiche pas, sélectionnez **Actualiser** à plusieurs reprises pendant plusieurs minutes. (Les journaux n’apparaissent pas immédiatement, car un certain temps est nécessaire au client Application Insights pour vider les journaux traités.)

   ![Journaux dans Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Fermez la fenêtre de console.

## <a name="deploy-as-a-webjob"></a>Déployer le projet en tant que tâche web

Dans cette section, vous allez déployer le projet en tant que tâche web. Vous allez le déployer vers une application App Service que vous avez [créée précédemment](#create-app-service-app-and-application-insights-instance). Pour tester le code pendant qu’il est exécuté dans Azure, vous devez déclencher une invocation de fonction en créant un message en file d’attente.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier en tant que tâche web Azure**.

1. Dans la boîte de dialogue **Ajouter une tâche web Azure**, sélectionnez **OK**.

   ![Ajouter une tâche web Azure](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio installe automatiquement un package NuGet pour la publication d’une tâche web.

1. À l’étape **Profil** de l’Assistant **Publier**, sélectionnez **Microsoft Azure App Service**.

   ![Boîte de dialogue Publier](./media/webjobs-sdk-get-started/publish-dialog.png)

1. Dans la boîte de dialogue **App Service**, sélectionnez **votre groupe de ressources > votre application App Service**, puis sélectionnez **OK**.

   ![Boîte de dialogue App Service](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. Une fois le profil de publication généré, sélectionnez **Publier**.

## <a name="trigger-the-function-in-azure"></a>Déclencher la fonction dans Azure

1. Assurez-vous que l’exécution n’est pas locale (fermez la fenêtre de console si elle est toujours ouverte). Dans le cas contraire, l’instance locale risque d’être la première à traiter les messages en file d’attente que vous créez.

1. Dans la page **File d’attente** de Visual Studio, ajoutez un message à la file d’attente, comme vous l’avez déjà fait.

1. Actualisez la page **File d’attente** ; le nouveau message disparaît, car il a été traité par la fonction qui s’exécute dans Azure.

   > [!TIP]
   > Si vous procédez au test dans Azure, utilisez le [mode de développement](webjobs-sdk-how-to.md#host-development-settings) pour vous assurer qu’une fonction de déclenchement de file d’attente est appelée immédiatement et éviter les retards dus à la [temporisation exponentielle de l’interrogation de la file d’attente](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Afficher les journaux dans Application Insights

1. Ouvrez le [portail Azure](https://portal.azure.com/), puis accédez à la ressource Application Insights.

1. Sélectionnez **Recherche**.

1. Si le message *Hello Azure !* ne s’affiche pas, sélectionnez **Actualiser** à plusieurs reprises pendant plusieurs minutes.

   Vous pouvez observer les journaux de la fonction exécutée dans une tâche web, y compris le texte *Hello Azure !* que vous avez entré dans la section précédente.

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

   Dans ce code, `queueTrigger` est une [expression de liaison](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), ce qui signifie qu’elle est résolue en une valeur différente lors de l’exécution.  Lors de l’exécution, son contenu est celui du message en file d’attente.

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

1. Exécutez le projet.

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

1. Exécutez le projet.

   Le message en file d’attente déclenche la fonction, qui lit l’objet blob, consigne sa longueur et crée un objet blob. La sortie de la console est identique, mais lorsque vous accédez à la fenêtre Conteneur d’objets blob et sélectionnez **Actualiser**, vous pouvez observer un nouvel objet blob nommé *copy-Program.cs*.

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment créer, exécuter et déployer un projet de Kit de développement logiciel (SDK) WebJobs.

Pour afficher tous les éléments d’un projet de Kit de développement logiciel (SDK) WebJobs, vous avez dû créer un projet à partir de zéro. Toutefois, lorsque vous créerez votre prochain projet, pensez à utiliser le modèle **Tâche web Azure** dans la catégorie **Cloud**. Ce modèle permet de créer un projet avec les packages NuGet et l’exemple de code déjà configuré. Vous devrez peut-être modifier l’exemple de code pour utiliser le nouveau framework de journalisation.

> [!div class="nextstepaction"]
> [En savoir plus sur le kit SDK WebJobs](webjobs-sdk-how-to.md)
