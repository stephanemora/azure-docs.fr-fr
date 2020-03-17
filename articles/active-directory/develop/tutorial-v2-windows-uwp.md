---
title: Bien démarrer avec la plateforme d’identités Microsoft pour les applications UWP | Azure
description: Découvrez comment les applications de plateforme Windows universelle (UWP) peuvent appeler une API qui nécessite des jetons d’accès provenant du point de terminaison de la plateforme d’identités Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 68473ff5a3faddd36bd4299dfdc882f679acd068
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129885"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Appeler l’API Microsoft Graph à partir d’une application de plateforme Windows universelle (XAML)

> [!div renderon="docs"]

Ce guide explique comment une application de plateforme Windows universelle (UWP) native peut demander un jeton d’accès. L’application appelle ensuite l’API Microsoft Graph. Ce guide s’applique également aux autres API qui utilisent des jetons d’accès reçus du point de terminaison de la plateforme d’identités Microsoft.

À la fin de ce guide, votre application appelle une API protégée à l’aide de comptes personnels. Outlook.com, live.com, etc. en sont des exemples. Votre application appelle également des comptes professionnels et scolaires de toute entreprise ou organisation qui utilise Azure Active Directory (Azure AD).

>[!NOTE]
> Ce guide nécessite Visual Studio avec Développement pour la plateforme Windows universelle. Pour obtenir des instructions sur le téléchargement et la configuration de Visual Studio afin de développer des applications de plateforme Windows universelle, consultez l’article [Préparation](https://docs.microsoft.com/windows/uwp/get-started/get-set-up).

>[!NOTE]
> Si vous êtes un nouvel utilisateur de la Plateforme d’identités Microsoft, nous vous recommandons de commencer avec le guide de démarrage rapide [Appeler l’API Microsoft Graph à partir d’une application de plateforme Windows universelle (UWP)](quickstart-v2-uwp.md).

## <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Ce guide crée un exemple d’application UWP qui interroge l’API Microsoft Graph. Pour ce scénario, un jeton est ajouté à des requêtes HTTP via l’en-tête d’autorisation. Microsoft Authentication Library (MSAL) gère les acquisitions et renouvellements de jetons.

## <a name="nuget-packages"></a>Packages NuGet

Ce guide utilise le package NuGet suivant :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Configuration de votre projet

Cette section fournit des instructions pas à pas pour intégrer une application .NET de bureau Windows (XAML) avec Se connecter avec Microsoft. L’application peut alors interroger les API web qui nécessitent un jeton, par exemple l’API Microsoft Graph.

Ce guide crée une application qui affiche un bouton qui permet d’interroger l’API Graph et un bouton destiné à se déconnecter. Elle affiche aussi des zones de texte qui contiennent les résultats des appels.

> [!NOTE]
> Vous préférez télécharger le projet Visual Studio de cet exemple au lieu de le créer ? [Téléchargez un projet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) et passez à l’étape d’[inscription d’application](#register-your-application "étape d’inscription d’application") pour configurer l’exemple de code avant son exécution.

### <a name="create-your-application"></a>Créer votre application

1. Ouvrez Visual Studio et sélectionnez **Créer un projet**.
1. Dans **Créer un nouveau projet**, choisissez **Application vide (Universal Windows)**  pour C#, puis sélectionnez **Suivant**.
1. Dans **Configurer votre nouveau projet**, nommez l’application, puis sélectionnez **Créer**.
1. Si vous y êtes invité, dans **Nouveau projet de plateforme Windows universelle**, sélectionnez une version pour les versions **Cible** et **Minimale**, puis sélectionnez **OK**.

   ![Versions minimale et cible](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Ajouter Microsoft Authentication Library à votre projet

1. Dans Visual Studio, sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.
1. Copiez et collez la commande suivante dans la fenêtre **Console du Gestionnaire de package** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Cette commande installe [Microsoft Authentication Library](https://aka.ms/msal-net). MSAL acquiert, met en cache et actualise les jetons utilisateur qui accèdent aux API protégées par la plateforme d’identités Microsoft.

### <a name="create-your-applications-ui"></a>Créer l’interface utilisateur de votre application

Visual Studio crée *MainPage.xaml* dans votre modèle de projet. Ouvrez ce fichier et remplacez le nœud **Grid** de votre application par le code suivant :

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Utiliser MSAL pour obtenir un jeton pour l’API Microsoft Graph

Cette section montre comment utiliser MSAL afin d’obtenir un jeton pour l’API Microsoft Graph. Apportez des modifications au fichier *MainPage.xaml.cs*.

1. Dans *MainPage.xaml.cs*, ajoutez les références suivantes :

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Remplacez votre classe `MainPage` par le code suivant :

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### Obtenir un jeton utilisateur de manière interactive<a name="more-information"></a>

La méthode `AcquireTokenInteractive` affiche une fenêtre qui invite les utilisateurs à se connecter. Les applications imposent généralement aux utilisateurs de se connecter de manière interactive la première fois qu’ils accèdent à une ressource protégée. Ils peuvent également avoir besoin de se connecter en cas d’échec d’une opération en mode silencieux pour obtenir un jeton, par exemple quand le mot de passe d’un utilisateur est arrivé à expiration.

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux

La méthode `AcquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Après que la méthode `AcquireTokenInteractive` s’est exécutée pour la première fois et a demandé à l’utilisateur des informations d’identification, utilisez la méthode `AcquireTokenSilent` pour demander des jetons pour les appels ultérieurs. Cette méthode acquiert les jetons en mode silencieux. MSAL gère le cache des jetons et leur renouvellement.

La méthode `AcquireTokenSilent` peut échouer. Cet échec peut notamment être dû à une déconnexion de l’utilisateur ou à la modification de son mot de passe sur un autre appareil. Quand MSAL détecte que le problème nécessite une action interactive, une exception `MsalUiRequiredException` est levée. Votre application peut gérer cette exception de deux manières :

* Votre application appelle immédiatement `AcquireTokenInteractive`. Cet appel invite l’utilisateur à se connecter. Normalement, cette approche est réservée aux applications en ligne qui n’ont pas de contenu hors connexion à la disposition de l’utilisateur. L’exemple créé avec cette installation guidée suit ce modèle. Vous le découvrez en action lors de la première exécution de l’exemple.

   Comme aucun utilisateur n’a encore utilisé l’application, `accounts.FirstOrDefault()` contient une valeur null et lève une exception `MsalUiRequiredException`.

   Le code contenu dans l’exemple gère alors l’exception en appelant `AcquireTokenInteractive`. Cet appel invite l’utilisateur à se connecter.

* Votre application présente une indication visuelle aux utilisateurs qu’ils doivent se connecter. Ils peuvent alors sélectionner le moment opportun pour se connecter. L’application peut réessayer `AcquireTokenSilent` ultérieurement. Employez cette approche quand les utilisateurs peuvent utiliser d’autres fonctionnalités de l’application sans interruption de service, par exemple lorsque le contenu hors connexion est disponible dans l’application. Dans ce cas, les utilisateurs peuvent décider à quel moment ils se connectent. L’application peut réessayer `AcquireTokenSilent` à la suite d’une indisponibilité temporaire du réseau.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

Ajoutez la nouvelle méthode suivante à *MainPage.xaml.cs* :

   ```csharp
   /// <summary>
   /// Perform an HTTP GET request to a URL using an HTTP Authorization header
   /// </summary>
   /// <param name="url">The URL</param>
   /// <param name="token">The token</param>
   /// <returns>String containing the results of the GET operation</returns>
   public async Task<string> GetHttpContentWithToken(string url, string token)
   {
       var httpClient = new System.Net.Http.HttpClient();
       System.Net.Http.HttpResponseMessage response;
       try
       {
           var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
           // Add the token in Authorization header
           request.Headers.Authorization = 
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
           response = await httpClient.SendAsync(request);
           var content = await response.Content.ReadAsStringAsync();
           return content;
       }
       catch (Exception ex)
        {
           return ex.ToString();
       }
    }
   ```

 Cette méthode effectue une requête `GET` à partir de l’API Graph en utilisant un en-tête `Authorization`.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Plus d’informations sur l’envoi d’un appel REST à une API protégée

Dans cet exemple d’application, la méthode `GetHttpContentWithToken` effectue une requête `GET` HTTP sur une ressource protégée qui nécessite un jeton. La méthode retourne ensuite le contenu à l’appelant. Elle ajoute le jeton acquis à l’en-tête **Autorisation HTTP**. Dans cet exemple, la ressource est le point de terminaison **me** de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur.

### <a name="add-a-method-to-sign-out-the-user"></a>Ajouter une méthode pour déconnecter l’utilisateur

Pour déconnecter l’utilisateur, ajoutez la méthode suivante au fichier *MainPage.xaml.cs* :

   ```csharp
   /// <summary>
   /// Sign out the current user
   /// </summary>
   private async void SignOutButton_Click(object sender, RoutedEventArgs e)
   {
       IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
       IAccount firstAccount = accounts.FirstOrDefault();

       try
       {
           await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
               ResultText.Text = "User has signed out";
               this.CallGraphButton.Visibility = Visibility.Visible;
                   this.SignOutButton.Visibility = Visibility.Collapsed;
               });
           }
           catch (MsalException ex)
           {
               ResultText.Text = $"Error signing out user: {ex.Message}";
           }
       }
   ```

> [!NOTE]
> MSAL.NET utilise des méthodes asynchrones pour acquérir des jetons ou manipuler des comptes. Vous devez prendre en charge les actions d’interface utilisateur dans le thread d’interface utilisateur. C’est ce qui explique la raison de l’appel `Dispatcher.RunAsync` et des précautions à observer pour appeler `ConfigureAwait(false)`.

#### Informations supplémentaires sur la déconnexion<a name="more-information-on-sign-out"></a>

La méthode `SignOutButton_Click` supprime l’utilisateur du cache d’utilisateur MSAL. Cette méthode permet d’indiquer efficacement à MSAL d’oublier l’utilisateur actuel. Une demande ultérieure d’acquisition d’un jeton n’aboutit que si elle est interactive.

L’application de cet exemple prend en charge un seul utilisateur, MSAL prend en charge les scénarios où l’utilisateur peut se connecter à plusieurs comptes. C’est le cas, par exemple, d’une application de messagerie hébergeant plusieurs comptes d’un même utilisateur.

### <a name="display-basic-token-information"></a>Afficher les informations de base du jeton

Pour afficher les informations de base du jeton, ajoutez la méthode suivante au fichier *MainPage.xaml.cs* :

   ```csharp
   /// <summary>
   /// Display basic information contained in the token. Needs to be called from the UI thread.
   /// </summary>
   private void DisplayBasicTokenInfo(AuthenticationResult authResult)
   {
       TokenInfoText.Text = "";
       if (authResult != null)
       {
           TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
           TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
       }
   }
   ```

#### Informations complémentaires<a name="more-information-1"></a>

Les jetons d’ID acquis en utilisant **OpenID Connect** contiennent aussi une petite partie des informations pertinentes pour l’utilisateur. `DisplayBasicTokenInfo` affiche les informations de base contenues dans le jeton, Ces informations se composent du nom d’affichage et de l’ID de l’utilisateur. Elles comprennent aussi la date d’expiration du jeton et la chaîne qui représente le jeton d’accès proprement dit. Si vous cliquez plusieurs fois sur le bouton **Call Microsoft Graph API** (Appeler l’API Microsoft Graph), vous pouvez constater que le même jeton a été réutilisé pour les demandes suivantes. Vous observez également la date d’expiration différée lorsque MSAL détermine qu’il est temps de renouveler le jeton.

### <a name="display-message"></a>Afficher un message

Ajoutez la nouvelle méthode suivante à *MainPage.xaml.cs* :

   ```csharp
   /// <summary>
   /// Displays a message in the ResultText. Can be called from any thread.
   /// </summary>
   private async Task DisplayMessageAsync(string message)
   {
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
            () =>
            {
                ResultText.Text = message;
            });
        }
   ```

## <a name="register-your-application"></a>Inscrivez votre application

Vous devez maintenant inscrire votre application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Azure Active Directory** > **Inscriptions des applications**.
1. Sélectionnez **Nouvelle inscription**. Entrez un nom d’application cohérent que les utilisateurs de l’application verrons, par exemple *App-UWP-appelant-MSGraph*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** , puis sélectionnez **Enregistrer** pour continuer.
1. Dans la page Vue d’ensemble, recherchez la valeur **ID d’application (client)** et copiez-la. Revenez dans Visual Studio, ouvrez *MainPage.xaml.cs* et remplacez la valeur de `ClientId` par cette valeur.

Configurez l’authentification pour votre application :

1. De retour sur le [portail Azure](https://portal.azure.com), sous **Gérer**, sélectionnez **Authentification**.
1. Dans la section **URI de redirection** | **URI de redirection suggérés pour les clients publics (mobile, bureau)** , cochez **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Sélectionnez **Enregistrer**.

Configurez des autorisations d’API pour votre application :

1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sélectionnez **Ajouter une autorisation** et vérifiez que vous avez bien sélectionné **API Microsoft**.
1. Sélectionnez **Microsoft Graph**.
1. Sélectionnez **Autorisations déléguées**, recherchez *User. Read*, puis vérifiez que **User. Read** est sélectionné.
1. Si vous avez apporté des modifications, sélectionnez **Ajouter des autorisations** pour les enregistrer.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Activer l’authentification intégrée sur des domaines fédérés (facultatif)

Pour activer l’authentification intégrée de Windows avec un domaine Azure AD fédéré, le manifeste de l’application doit activer des fonctionnalités supplémentaires. Revenez dans votre application dans Visual Studio.

1. Ouvrez *Package.appxmanifest*.
1. Sélectionnez **Capacités** et activez les paramètres suivants :

   * **Authentification en entreprise**
   * **Réseaux privés (client et serveur)**
   * **Certificats utilisateur partagés**

> [!IMPORTANT]
> L’[Authentification Windows intégrée](https://aka.ms/msal-net-iwa) n’est pas configurée par défaut pour cet exemple. Les applications qui demandent des capacités `Enterprise Authentication` ou `Shared User Certificates` nécessitent un niveau de vérification supérieur de Microsoft Store. Tous les développeurs ne souhaitent pas effectuer la vérification de niveau supérieur. Activez ce paramètre uniquement si vous avez besoin d’utiliser l’authentification intégrée de Windows avec un domaine Azure AD fédéré.

## <a name="test-your-code"></a>Test de votre code

Pour tester votre application, appuyez sur F5 afin d’exécuter votre projet dans Visual Studio. Votre fenêtre principale s’affiche :

![Interface utilisateur de l’application](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Lorsque vous êtes prêt à passer aux tests, sélectionnez **Call Microsoft Graph API** (Appeler l’API Microsoft Graph). Utilisez ensuite un compte professionnel Azure AD ou un compte Microsoft (par exemple, live.com ou outlook.com) pour vous connecter. La première fois qu’un utilisateur l’exécute, l’application affiche une fenêtre demandant à l’utilisateur de se connecter.

### <a name="consent"></a>Consentement

La première fois que vous vous connectez à votre application, un écran de consentement semblable à ce qui suit s’affiche. Sélectionnez **Oui** pour donner explicitement votre consentement pour l’accès :

![Écran de consentement d’accès](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Résultats attendus

Les informations de profil utilisateur retournées par l’appel de l’API Microsoft Graph s’affichent dans l’écran **API Call Results** (Résultats de l’appel d’API) :

![Écran API Call Results (Résultats de l’appel d’API)](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Des informations de base sur le jeton obtenu via `AcquireTokenInteractive` ou `AcquireTokenSilent` s’affichent également dans la zone **Token Info** (Informations sur le jeton) :

|Propriété  |Format  |Description |
|---------|---------|---------|
|`Username` |`user@domain.com` |Nom qui identifie l’utilisateur.|
|`Token Expires` |`DateTime` |L’heure d’expiration du jeton. MSAL repousse la date d’expiration en renouvelant le jeton si nécessaire.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue `user.read` pour lire le profil d’un utilisateur. Par défaut, cette étendue est ajoutée à toutes les applications inscrites sur le portail d’inscription de l’application. D’autres API pour Microsoft Graph et des API personnalisées pour votre serveur back-end peuvent nécessiter des étendues supplémentaires. Par exemple, l’API Microsoft Graph nécessite l’étendue `Calendars.Read` pour lister les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée `Calendars.Read` aux informations d’inscription de l’application. Ajoutez ensuite l’étendue `Calendars.Read` à l’appel `acquireTokenSilent`.

> [!NOTE]
> Les utilisateurs peuvent être invités à donner des consentements supplémentaires lorsque vous augmentez le nombre d’étendues.

## <a name="known-issues"></a>Problèmes connus

### <a name="issue-1"></a>Problème 1

Vous recevez l’un des messages d’erreur suivants quand vous vous connectez à votre application dans un domaine Azure AD fédéré :

* Aucun certificat client valide trouvé dans la demande.
* Aucun certificat valide trouvé dans le magasin de certificats de l’utilisateur.
* Réessayez en choisissant une autre méthode d’authentification.

Cause : les fonctionnalités d’entreprise et de certificats ne sont pas activées.

Solution : Suivez les étapes décrites dans [Activer l’authentification intégrée sur des domaines fédérés (facultatif)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problème 2

Vous activez l’[authentification intégrée sur des domaines fédérés](#enable-integrated-authentication-on-federated-domains-optional) et essayez d’utiliser Windows Hello sur un ordinateur Windows 10 pour vous connecter à un environnement où l’authentification multifacteur est configurée. La liste des certificats s’affiche. Toutefois, si vous choisissez d’utiliser votre code confidentiel, la fenêtre correspondante ne s’affiche jamais.

Cause : ce problème est une limitation connue du répartiteur d’authentification web dans les applications UWP qui s’exécutent sur les appareils de bureau Windows 10. Il fonctionne correctement sur Windows 10 Mobile.

Solution de contournement : sélectionnez **Connexion avec d’autres options**, puis **Sign in with a username and password** (Se connecter avec un nom d’utilisateur et un mot de passe). Sélectionnez **Provide your password** (Indiquer votre mot de passe), puis passez au processus d’authentification par téléphone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Aidez-nous à améliorer la plateforme des identités Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions :

> [!div class="nextstepaction"]
> [Enquête sur la plateforme des identités Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
