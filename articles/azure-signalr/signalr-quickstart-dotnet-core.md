---
title: Démarrage rapide pour apprendre à utiliser Azure SignalR Service | Microsoft Docs
description: Un démarrage rapide pour utiliser Azure SignalR Service afin de créer une salle de conversation avec les applications MVC ASP.NET Core.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: 78e164b566194fcfe952e3ad59dd3d228f90d193
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-a-chat-room-with-signalr-service"></a>Guide de démarrage rapide : créer une salle de conversation avec SignalR Service

Azure SignalR Service est un service Azure qui permet aux développeurs de créer facilement des applications web avec des fonctionnalités en temps réel. Ce service est basé sur [SignalR for ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

Cet article explique comment démarrer avec Azure SignalR Service. Dans ce démarrage rapide, vous allez créer une application de conversation à l’aide d’une application Web App MVC ASP.NET Core. Cette application établira une connexion avec votre ressource Azure SignalR Service pour activer les mises à jour de contenu en temps réel. Vous hébergerez l’application web localement et vous connecterez à plusieurs clients de navigateur. Chaque client pourra envoyer (push) les mises à jour de contenus à tous les autres clients. 


Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce démarrage rapide. Toutefois, [Visual Studio Code](https://code.visualstudio.com/) est une excellente option disponible sur les plateformes Windows, macOS et Linux.

Le code de ce tutoriel est disponible au téléchargement dans le [référentiel AzureSignalR-samples GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom).  En outre, la création des ressources Azure utilisées dans ce démarrage rapide peut être effectuée à l’aide du [script Créer un SignalR Service](scripts/signalr-cli-create-service.md).

![Démarrage rapide complet local](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis


* [Installer le kit de développement logiciel (SDK) .NET Core](https://www.microsoft.com/net/download/windows)
* Télécharger ou cloner le référentiel GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

## <a name="create-an-azure-signalr-resource"></a>Créer une ressource Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core

Dans cette section, vous utilisez l[’interface de ligne de commande (CLI) .NET Core](https://docs.microsoft.com/dotnet/core/tools/) pour créer un nouveau projet d’application Web App MVC ASP.NET Core. Par rapport à Visual Studio, l’interface CLI .NET Core offre l’avantage d’être disponible sur les plateformes Windows, macOS et Linux. 

1. Créez un nouveau dossier pour votre projet. Ce guide de démarrage rapide utilise le dossier *E:\Testing\chattest*.

2. Dans le nouveau dossier, exécutez la commande suivante pour créer un projet d’application Web MVC ASP.NET Core :

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Ajouter Secret Manager au projet

Dans cette section, vous allez ajouter l’outil [Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) à votre projet. L’outil Secret Manager stocke les données sensibles pour les travaux de développement à l’extérieur de l’arborescence de votre projet. Cette approche empêche le partage accidentel des secrets d’une application au sein du code source.

1. Ouvrez votre fichier *.csproj*. Ajoutez un élément `DotNetCliToolReference` afin d’inclure *Microsoft.Extensions.SecretManager.Tools*. Ajoutez également un élément `UserSecretsId`, comme indiqué ci-dessous, puis enregistrez le fichier.

    *chattest.csproj:*

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Ajouter Azure SignalR à l’application web

1. Ajoutez une référence au package NuGet `Microsoft.Azure.SignalR` en exécutant la commande suivante :

        dotnet add package Microsoft.Azure.SignalR -v 1.0.0-preview-10007

2. Exécutez la commande suivante pour restaurer les packages de votre projet.

        dotnet restore

3. Ajoutez un secret nommé *Azure:SignalR:ConnectionString* à Secret Manager. Ce secret contiendra la chaîne de connexion pour accéder à votre ressource SignalR Service. *Azure:SignalR:ConnectionString* est la clé de configuration par défaut recherchée par SignalR pour établir une connexion. Dans la commande ci-dessous, remplacez la valeur par la chaîne de connexion de votre ressource SignalR Service.

    Cette commande doit être exécutée dans le même répertoire que le fichier *.csproj*.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString Endpoint=<Your endpoint>;AccessKey=<Your access key>;    
    ```

    Secret Manager servira uniquement à tester l’application web lorsqu’elle est hébergée localement. Dans un prochain tutoriel, vous allez déployer l’application web de conversation dans Azure. Une fois l’application web déployée dans Azure, vous allez utiliser un paramètre d’application au lieu de stocker la chaîne de connexion avec Secret Manager.

4. Ouvrez *Startup.cs* et mettez à jour la méthode `ConfigureServices` afin d’utiliser Azure SignalR Service en appelant la méthode `services.AddSignalR().AddAzureSignalR()` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    En ne passant pas le paramètre à `AddAzureSignalR()`, ce code utilise la clé de configuration par défaut, *Azure:SignalR:ConnectionString*, pour la chaîne de connexion de la ressource SignalR Service.

5. Également dans *Startup.cs*, mettez à jour la méthode `Configure` en remplaçant l’appel à `app.UseStaticFiles()` par le code suivant, puis enregistrez le fichier.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Ajouter une classe de concentrateur

Dans SignalR, un concentrateur est un composant qui expose un ensemble de méthodes pouvant être appelées à partir du client. Dans cette section, vous définissez une classe de concentrateur avec deux méthodes : 

* `Broadcast` : cette méthode envoie un message à tous les clients.
* `Echo` : cette méthode renvoie un message à l’appelant.

Ces deux méthodes utilisent l’interface `Clients` fournie par le Kit de développement logiciel (SDK) SignalR ASP.NET Core. Cette interface vous permet d’accéder à tous les clients connectés et d’envoyer (push) ainsi le contenu à vos clients.

1. Dans le répertoire de votre projet, ajoutez un nouveau dossier nommé *Hub*. Ajoutez un nouveau fichier de code de concentrateur nommé *Chat.cs* au nouveau dossier.

2. Ajoutez le code suivant au fichier *Chat.cs* pour définir votre classe de concentrateur, puis enregistrez le fichier. 

    Mettez à jour l’espace de noms pour cette classe si vous avez utilisé un nom de projet différent de *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-web-app-client-interface"></a>Ajouter l’interface client d’application web

L’interface utilisateur du client pour cette application de salle de conversation sera composée d’éléments HTML et JavaScript enregistrés dans un fichier *index.html* figurant dans le répertoire *wwwroot*.

Copiez le fichier *index.html*, puis les dossiers *css* et *scripts* du répertoire *wwwroot* du [référentiel d’exemples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) dans le répertoire *wwwroot* de votre projet.

Code principal du fichier *index.html* : 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

Le code du fichier *index.html* appelle `HubConnectionBuilder.build()` pour établir une connexion HTTP à la ressource Azure SignalR.

Si elle est établie, cette connexion est passée à `bindConnectionMessage`, qui ajoute des gestionnaires d’événements pour le contenu entrant envoyé (push) au client. 

`HubConnection.start()` lance la communication avec le concentrateur. Une fois la communication lancée, `onConnected()` ajoute les gestionnaires d’événements de bouton. Ces gestionnaires utilisent la connexion pour permettre à ce client d’envoyer (push) des mises à jour de contenu à tous les clients connectés.

## <a name="add-a-development-runtime-profile"></a>Ajouter un profil d’exécution de développement

Dans cette section, vous allez ajouter un environnement d’exécution de développement pour ASP.NET Core. Pour plus d’informations sur l’environnement d’exécution ASP.NET Core, consultez [Utilisation de plusieurs environnements dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Créer un nouveau dossier dans votre projet nommé *Properties*.

2. Ajoutez un nouveau fichier nommé *launchSettings.json* dans le dossier, avec le contenu suivant, puis enregistrez le fichier.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Pour générer l’application à l’aide de l’interface CLI .NET Core, exécutez la commande suivante dans l’interpréteur de commandes :

        dotnet build

2. Une fois la version terminée, exécutez la commande suivante afin d’exécuter l’application web en local :

        dotnet run

    L’application sera hébergée localement sur le port 5000, tel que configuré dans notre profil d’exécution de développement :

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Lancez deux fenêtres de navigateur, puis accédez à `http://localhost:5000` avec chaque navigateur. Vous serez invité à entrer votre nom. Entrez un nom de client pour les clients, puis testez l’envoi (push) d’un message entre les deux clients à l’aide du bouton **Envoyer**.

    ![Démarrage rapide complet local](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez d’exécuter le tutoriel suivant, vous pouvez conserver les ressources créées dans ce démarrage rapide.

Sinon, si l’exemple d’application de démarrage rapide était votre dernière opération, vous pouvez supprimer les ressources Azure créées dans ce démarrage rapide afin d’éviter tout frais. 

> [!IMPORTANT]
> La suppression d’un groupe de ressources est définitive ; le groupe de ressources et l’ensemble des ressources qu’il contient sont supprimés de manière permanente. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé les ressources pour l’hébergement de cet exemple dans un groupe de ressources existant contenant des ressources que vous souhaitez conserver, vous pouvez supprimer chaque ressource individuellement à partir de son panneau respectif, au lieu de supprimer l’intégralité du groupe de ressources.
> 
> 

Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.

Dans la zone de texte **Filtrer par nom.**, saisissez le nom de votre groupe de ressources. Les instructions relatives à ce démarrage rapide utilisaient un groupe de ressources nommé *SignalRTestResources*. Sur votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

   
![Supprimer](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez le nom de votre groupe de ressources pour confirmer, puis cliquez sur **Supprimer**.
   
Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.



## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé une nouvelle ressource Azure SignalR Service et l’avez utilisée avec une application Web ASP.NET Core pour envoyer (push) des mises à jour de contenu en temps réel à plusieurs clients connectés. Pour en savoir plus sur l’utilisation d’Azure SignalR Service, accédez au tutoriel suivant, qui présente l’authentification.

> [!div class="nextstepaction"]
> [Authentification par Azure SignalR Service](./signalr-authenticate-oauth.md)


