---
title: 'Tutoriel : Générer une application de conversation Blazor Server - Azure SignalR'
description: Dans ce tutoriel, vous allez apprendre à générer et à modifier une application Blazor Server avec Azure SignalR Service.
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 5ed5107cadcfbf247b79c18a6a2e391ab3043565
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331823"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Tutoriel : Créer une application de conversation sur un serveur Blazor Server

Ce tutoriel vous montre comment créer et modifier une application Blazor Server. Vous découvrirez comment effectuer les actions suivantes :
> [!div class="checklist"] 
> * Créer une salle de conversation simple avec le modèle d’application Blazor Server.
> * Utiliser des composants Razor.
> * Utiliser la gestion des événements et la liaison de données dans des composants Razor.
> * Effectuer un déploiement rapide sur Azure App Service avec Visual Studio.
> * Migrer le service SignalR local vers Azure SignalR Service.

## <a name="prerequisites"></a>Prérequis

* Installer le [SDK .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) (Version ultérieure à la version 3.0.100)
* Installer [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (Version ultérieure à la version 16.3)


[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Générer une salle de conversation locale dans une application Blazor Server

À compter de Visual Studio 2019 version 16.2.0, Azure SignalR Service est intégré au processus de publication d’application web pour faciliter la gestion des dépendances entre une application web et le service SignalR. Vous pouvez tester simultanément l’utilisation d’une instance du service SignalR local dans un environnement de développement local et l’utilisation d’Azure SignalR Service pour Azure App Service sans aucun changement de code.

1. Créez une application de conversation Blazor :
   1. Dans Visual Studio, choisissez **Créer un projet**. 
   1. Sélectionnez **Application Blazor**. 
   1. Nommez l’application et choisissez un dossier. 
   1. Sélectionnez le modèle **Application Blazor Server**.
    
       > [!NOTE]
       > Vérifiez que vous avez déjà installé le SDK .NET Core 3.0+ pour permettre à Visual Studio de reconnaître correctement le framework cible.
   
       [ ![Dans Créer un projet, sélectionnez le modèle d’application Blazor.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   5. Vous pouvez également créer un projet en exécutant la commande [`dotnet new`](/dotnet/core/tools/dotnet-new) dans l’interface CLI .NET :
   
       ```dotnetcli
       dotnet new blazorserver -o BlazorChat
       ```
   
1. Ajoutez un nouveau fichier C# nommé `BlazorChatSampleHub.cs` et créez une classe `BlazorSampleHub` dérivant de la classe `Hub` pour l’application de conversation. Pour plus d’informations sur la création de hubs, consultez [Créer et utiliser des hubs](/aspnet/core/signalr/hubs#create-and-use-hubs). 
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Ajoutez un point de terminaison pour le hub dans la méthode `Startup.Configure()`.
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Installez le package `Microsoft.AspNetCore.SignalR.Client` pour utiliser le client SignalR.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Créez un [composant Razor](/aspnet/core/blazor/components/) nommé `ChatRoom.razor` sous le dossier `Pages` pour implémenter le client SignalR. Suivez les étapes ci-dessous ou utilisez le fichier [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Ajoutez la directive [`@page`](/aspnet/core/mvc/views/razor#page) et les instructions using. Utilisez la directive [`@inject`](/aspnet/core/mvc/views/razor#inject) pour injecter le service [`NavigationManager`](/aspnet/core/blazor/fundamentals/routing#uri-and-navigation-state-helpers).
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Dans la section `@code`, ajoutez les membres suivants au nouveau client SignalR pour envoyer et recevoir des messages.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Ajoutez la balise d’interface utilisateur avant la section `@code` pour interagir avec le client SignalR.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Mettez à jour le composant `NavMenu.razor` pour insérer un nouveau composant `NavLink` à lier à la salle de conversation sous `NavMenuCssClass`.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Ajoutez quelques classes CSS au fichier `site.css` pour styliser les éléments d’interface utilisateur dans la page de conversation.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Appuyez sur <kbd>F5</kbd> pour exécuter l'application. À présent, vous pouvez lancer la conversation :

   [ ![Une conversation animée entre Bob et Alice s’affiche. Alice dit Bonjour, Bob dit Salut.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Publication dans Azure

Quand vous déployez l’application Blazor sur Azure App Service, nous vous recommandons d’utiliser [Azure SignalR Service](/aspnet/core/signalr/scale#azure-signalr-service). Azure SingalR Service permet d’effectuer un scale-up d’une application Blazor Server sur un grand nombre de connexions SignalR simultanées. De plus, la portée mondiale et les centres de données hautes performances du service SignalR contribuent à réduire considérablement la latence due aux zones géographiques.

> [!IMPORTANT]
> Dans une application Blazor Server, les états de l’interface utilisateur sont conservés côté serveur, ce qui signifie qu’une session de serveur persistante est nécessaire pour conserver un état. S’il existe un seul serveur d’applications, les sessions persistantes sont assurées de fait. Toutefois, s’il existe plusieurs serveurs d’applications, il y a de fortes chances pour que la négociation et la connexion du client passent par des serveurs différents, ce qui risque d’entraîner une gestion incohérente des états de l’interface utilisateur dans une application Blazor. Il est donc recommandé d’activer les sessions de serveur persistantes comme indiqué ci-dessous dans *appsettings.json* :
>
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Cliquez avec le bouton droit sur le projet et accédez à **Publier**. Utilisez les paramètres suivants :
   * **Cible** : Azure
   * **Cible spécifique** : Tous les types **Azure App Service** sont pris en charge.
   * **App service** : Créez ou sélectionnez l’instance App Service.

   [ ![L’animation montre la sélection d’Azure comme cible, puis d’Azure App Service comme cible spécifique.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Ajoutez la dépendance Azure SignalR Service.

   Après la création du profil de publication, un message de recommandation s’affiche pour ajouter Azure SignalR Service sous **Dépendances de service**. Sélectionnez **Configurer** pour créer une instante Azure SignalR Service ou en sélectionner une existante dans le volet.

   [ ![Dans Publier, le lien vers Configurer est mis en surbrillance.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   La dépendance de service va effectuer les activités suivantes pour permettre à votre application de basculer automatiquement vers Azure SignalR Service, une fois sur Azure :

   * Mettez à jour [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) pour utiliser Azure SignalR Service.
   * Ajouter une référence de package NuGet Azure SignalR Service.
   * Mettre à jour les propriétés de profil pour enregistrer les paramètres de dépendance.
   * Configurer le magasin de secrets conformément à votre choix.
   * Ajouter la configuration dans *appsettings.json* pour que votre application cible Azure SignalR Service.

   [ ![Dans Récapitulatif des changements, les cases à cocher permettent de sélectionner toutes les dépendances.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Publiez l’application.

   Votre application est maintenant prête à être publiée. Une fois le processus de publication terminé, l’application se lance automatiquement dans un navigateur.
 
   > [!NOTE]
   > Elle peut nécessiter un certain temps pour démarrer en raison de la latence observée au démarrage du déploiement d’Azure App Service. Vous pouvez utiliser les outils de débogage du navigateur (généralement en appuyant sur <kbd>F12</kbd>) pour vérifier que le trafic a été redirigé vers Azure SignalR Service.

   [ ![L’exemple de conversation Blazor SignalR est associé à une zone de texte pour votre nom et à un bouton Chat! (Conversation !) pour démarrer une conversation.](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/asrs/qsblazor)

## <a name="enable-azure-signalr-service-for-local-development"></a>Activer Azure SignalR Service pour un développement local

1. Ajoutez une référence au SDK Azure SignalR à l’aide de la commande suivante.

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Ajoutez un appel à `AddAzureSingalR()` dans `Startup.ConfigureServices()` comme illustré ci-dessous.

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Configurez la chaîne de connexion Azure SignalR Service dans *appsettings.json* ou en utilisant l’outil [Secret Manager](/aspnet/core/security/app-secrets?tabs=visual-studio#secret-manager).

> [!NOTE]
> L’étape 2 peut être remplacée par la configuration d’[assemblys de démarrage d’hébergement](/aspnet/core/fundamentals/host/platform-specific-configuration) afin d’utiliser le SDK SignalR.
>
> 1. Ajoutez la configuration pour activer Azure SignalR Service dans *appsettings.json* :
>
>     ```json
>     "Azure": {
>       "SignalR": {
>         "Enabled": true,
>         "ConnectionString": <your-connection-string>       
>       }
>     }
>    
>    ```
>
> 1. Affectez l’assembly de démarrage d’hébergement pour utiliser le SDK Azure SignalR. Modifiez *launchSettings.json* et ajoutez une configuration comme dans l’exemple suivant dans `environmentVariables` :
>
>     ```json
>    "environmentVariables": {
>         ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>  
>     ```
>

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer les ressources créées à l’occasion de ce tutoriel, supprimez le groupe de ressources par le biais du portail Azure.

## <a name="additional-resources"></a>Ressources supplémentaires

* [ASP.NET Core Blazor](/aspnet/core/blazor)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une salle de conversation simple avec le modèle d’application Blazor Server.
> * Utiliser des composants Razor.
> * Utiliser la gestion des événements et la liaison de données dans des composants Razor.
> * Effectuer un déploiement rapide sur Azure App Service avec Visual Studio.
> * Migrer le service SignalR local vers Azure SignalR Service.

En savoir plus sur la haute disponibilité :
> [!div class="nextstepaction"]
> [Résilience et récupération d’urgence](signalr-concept-disaster-recovery.md)
