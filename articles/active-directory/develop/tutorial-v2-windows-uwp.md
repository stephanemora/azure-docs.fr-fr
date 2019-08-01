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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae3f9f2f4a9f6966d3453123773887c244cf4e2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334060"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Appeler l’API Microsoft Graph à partir d’une application de plateforme Windows universelle (XAML)

> [!div renderon="docs"]

Ce guide explique comment une application de plateforme Windows universelle (UWP) native peut demander un jeton d’accès, puis appeler l’API Microsoft Graph. Ce guide s’applique également aux autres API qui utilisent des jetons d’accès reçus du point de terminaison de la plateforme d’identités Microsoft.

À la fin de ce guide, votre application appelle une API protégée à l’aide de comptes personnels. Outlook.com, live.com, etc. en sont des exemples. Votre application appelle également des comptes professionnels et scolaires de toute entreprise ou organisation qui utilise Azure Active Directory (Azure AD).

>[!NOTE]
> Ce guide requiert de disposer de Visual Studio 2017 avec le Développement pour la plateforme Windows universelle. Pour obtenir des instructions sur le téléchargement et la configuration de Visual Studio afin de développer des applications de plateforme Windows universelle, consultez l’article [Préparation](https://docs.microsoft.com/windows/uwp/get-started/get-set-up).

## <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Ce guide crée un exemple d’application UWP qui interroge l’API Microsoft Graph ou une API web recevant des jetons du point de terminaison de la plateforme d’identités Microsoft. Pour ce scénario, un jeton est ajouté aux requêtes HTTP via l’en-tête d’autorisation. Microsoft Authentication Library (MSAL) gère les acquisitions et renouvellements de jetons.

## <a name="nuget-packages"></a>Packages NuGet

Ce guide utilise les packages NuGet suivants :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Configuration de votre projet

Cette section fournit des instructions détaillées pour intégrer une application .NET de bureau Windows (XAML) avec *Sign-In with Microsoft* (Se connecter avec Microsoft). Elle peut ensuite interroger les API web qui requièrent un jeton, par exemple l’API Microsoft Graph.

Avec ce guide, vous allez créer une application qui affiche un bouton permettant d’interroger l’API Graph, un bouton de déconnexion, et des zones de texte qui affichent les résultats des appels.

> [!NOTE]
> Préférez-vous télécharger le projet Visual Studio de cet exemple ? [Téléchargez un projet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) et passez à l’étape [d’inscription d’application](#register-your-application "l’étape d’inscription d’application") pour configurer l’exemple de code avant son exécution.

### <a name="create-your-application"></a>Créer votre application

1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.
2. Sous **Modèles**, sélectionnez **Visual C#** .
3. Sélectionnez **Application vide (Universal Windows)** .
4. Nommez l’application, puis sélectionnez **OK**.
5. Si vous y êtes invité, sélectionnez une version pour les versions **Cible** et **Minimale**, puis sélectionnez **OK**.

    >![Versions minimale et cible](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Ajouter Microsoft Authentication Library à votre projet
1. Dans Visual Studio, sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.
2. Copiez et collez la commande suivante dans la fenêtre **Console du Gestionnaire de package** :

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Cette commande installe [Microsoft Authentication Library](https://aka.ms/msal-net). MSAL acquiert, met en cache et actualise les jetons utilisateur qui accèdent aux API protégées par la plateforme d’identités Microsoft.

## <a name="create-your-applications-ui"></a>Créer l’interface utilisateur de votre application

Un fichier **MainPage.xaml** est créé automatiquement dans le cadre de votre modèle de projet. Ouvrez ce fichier, puis suivez les instructions :

* Remplacez le nœud **Grille** de votre application par le code suivant :

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Utiliser MSAL pour obtenir un jeton pour l’API Microsoft Graph

Cette section montre comment utiliser MSAL afin d’obtenir un jeton pour l’API Microsoft Graph.

1.  Dans le fichier **MainPage.xaml.cs**, ajoutez la référence de MSAL à la classe :

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Remplacez le code de la classe <code>MainPage</code> par le code suivant :

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
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
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
    ```

### <a name="more-information"></a>Plus d’informations

#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive

L’appel de la méthode `AcquireTokenInteractive` affiche une fenêtre invitant les utilisateurs à se connecter. Les applications requièrent généralement que les utilisateurs se connectent de manière interactive la première fois qu’ils cherchent à accéder à une ressource protégée. Ils peuvent également avoir besoin de se connecter en cas d’échec d’une opération en mode silencieux pour obtenir un jeton, par exemple lorsque le mot de passe d’un utilisateur est arrivé à expiration.

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux

La méthode `AcquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Après que la méthode `AcquireTokenInteractive` a été exécutée une première fois et que l’utilisateur a été invité à entrer les informations d’identification, il convient d’utiliser la méthode `AcquireTokenSilent` pour demander des jetons dans les appels suivants, car celle-ci permet d’acquérir des jetons en mode silencieux. MSAL gère la mise en cache et le renouvellement des jetons.

La méthode `AcquireTokenSilent` peut échouer. Cet échec peut être dû à une déconnexion de l’utilisateur ou à la modification de son mot de passe sur un autre appareil. Quand la bibliothèque MSAL détecte que le problème peut être résolu par une intervention interactive, elle déclenche une exception `MsalUiRequiredException`. Votre application peut gérer cette exception de deux manières :

* Elle peut appeler immédiatement `AcquireTokenInteractive`. Cet appel invite l’utilisateur à se connecter. Normalement, ce modèle est utilisé dans des applications en ligne où aucun contenu hors connexion n’est disponible pour l’utilisateur. L’exemple créé avec cette installation guidée suit ce modèle. Vous le découvrez en action lors de la première exécution de l’exemple.
  * Aucun utilisateur n’ayant encore utilisé l’application, `accounts.FirstOrDefault()` contient une valeur null, et une exception `MsalUiRequiredException` est levée.
  * Le code contenu dans l’exemple gère alors l’exception en appelant `AcquireTokenInteractive`. Cet appel invite l’utilisateur à se connecter.

* Elle peut également présenter une indication visuelle aux utilisateurs montrant qu’une connexion interactive est requise. Ils peuvent alors sélectionner le moment opportun pour se connecter. L’application peut également effectuer une nouvelle tentative de `AcquireTokenSilent` ultérieurement. Ce modèle est souvent utilisé lorsque les utilisateurs peuvent avoir recours à d’autres fonctionnalités de l’application sans interruption de service, par exemple lorsque le contenu hors connexion est disponible dans l’application. Dans ce cas, les utilisateurs peuvent décider de se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes. L’application peut également décider d’effectuer une nouvelle tentative de `AcquireTokenSilent` une fois le réseau restauré après une indisponibilité temporaire.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

* Ajoutez la nouvelle méthode suivante à au fichier **MainPage.xaml.cs**. Cette méthode permet d’envoyer une demande `GET` à l’API Graph à l’aide d’un en-tête `Authorization` :

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Plus d’informations sur l’envoi d’un appel REST à une API protégée

Dans cet exemple d’application, la méthode `GetHttpContentWithToken` est utilisée pour envoyer une demande HTTP `GET` à une ressource protégée qui requiert un jeton. La méthode retourne ensuite le contenu à l’appelant. Elle ajoute le jeton acquis à l’en-tête **Autorisation HTTP**. Dans cet exemple, la ressource est le point de terminaison **me** de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Ajouter une méthode pour déconnecter l’utilisateur

* Pour déconnecter l’utilisateur, ajoutez la méthode suivante au fichier **MainPage.xaml.cs** :

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET utilise des méthodes asynchrones pour acquérir des jetons ou manipuler des comptes. Vous devez donc veiller à effectuer les actions d’interface utilisateur dans le thread d’interface utilisateur, ce qui explique l’utilisation de `Dispatcher.RunAsync` et l’appel de `ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>En savoir plus sur la déconnexion

La méthode `SignOutButton_Click` supprime l’utilisateur du cache d’utilisateur MSAL. Cette méthode permet d’indiquer efficacement à MSAL d’oublier l’utilisateur actuel. Une demande ultérieure d’acquisition d’un jeton aboutit uniquement si elle est interactive.
L’application de cet exemple prend en charge un seul utilisateur, mais MSAL prend en charge des scénarios dans lesquels plusieurs comptes peuvent être connectés en même temps. C’est le cas, par exemple, d’une application de messagerie hébergeant plusieurs comptes d’un même utilisateur.

## <a name="display-basic-token-information"></a>Afficher les informations de base du jeton

* Pour afficher les informations de base du jeton, ajoutez la méthode suivante au fichier **MainPage.xaml.cs** :

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
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

### <a name="more-information"></a>Plus d’informations

Les jetons d’ID acquis via **OpenID Connect** contiennent également un petit sous-ensemble d’informations qui sont pertinentes pour l’utilisateur. `DisplayBasicTokenInfo` affiche les informations de base contenues dans le jeton, par exemple, le nom d’affichage et l’ID de l’utilisateur, la date d’expiration du jeton et la chaîne qui représente le jeton d’accès proprement dit. Si vous cliquez plusieurs fois sur le bouton **Call Microsoft Graph API** (Appeler l’API Microsoft Graph), vous observerez que le même jeton a été réutilisé pour les demandes suivantes. Vous observez également la date d’expiration différée lorsque MSAL détermine qu’il est temps de renouveler le jeton.

## <a name="register-your-application"></a>Inscrivez votre application

Maintenant, vous devez inscrire votre application dans le portail d’inscription des applications de Microsoft :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte est présent dans plusieurs locataires Azure AD, sélectionnez `Directory + Subscription` en haut à droite dans le menu en haut de la page, puis basculez votre session de portail vers le locataire Azure AD souhaité.
1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `UWP-App-calling-MSGraph`.
   - Dans la section **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** .
   - Sélectionnez **Inscrire** pour créer l’application.
1. Dans la page **Vue d’ensemble** de l’application, recherchez la valeur **ID d’application (client)** et notez-la. Revenez dans Visual Studio, ouvrez le fichier **MainPage.xaml.cs** et remplacez la valeur de ClientId par l’ID d’application que vous venez de noter :
1. Dans la liste des pages de l’application, sélectionnez **Authentification**.
   1. Dans la section **URI de redirection**, dans la liste des URI de redirection :
   1. Dans la colonne **TYPE**, sélectionnez **Client public (mobile et bureau)** .
   1. Entrez `urn:ietf:wg:oauth:2.0:oob` dans la colonne **URI de redirection**.
1. Sélectionnez **Enregistrer**.
1. Dans la liste des pages de l’application, sélectionnez **Autorisations de l’API**
   - Cliquez sur le bouton **Ajouter une autorisation**.
   - Vérifiez ensuite que l’onglet **API Microsoft** est sélectionné
   - Dans la section *API Microsoft couramment utilisées*, cliquez sur **Microsoft Graph**
   - Dans la section **Autorisations déléguées**, vérifiez que les autorisations appropriées sont cochées : **User.Read**. Utilisez la zone de recherche au besoin.
   - Sélectionnez le bouton **Ajouter des autorisations**

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Activer l’authentification intégrée sur des domaines fédérés (facultatif)

Pour activer l’authentification intégrée de Windows avec un domaine Azure AD fédéré, le manifeste de l’application doit activer des fonctionnalités supplémentaires :

1. Double-cliquez sur **Package.appxmanifest**.
2. Sélectionnez l’onglet **Capacités**, puis assurez-vous que les paramètres suivants sont activés :

    - Authentification en entreprise
    - Réseaux privés (client et serveur)
    - Certificats utilisateur partagés

> [!IMPORTANT]
> L’[Authentification Windows intégrée](https://aka.ms/msal-net-iwa) n’est pas configurée par défaut pour cet exemple. Les applications qui demandent les fonctionnalités *Authentification en entreprise* ou *Certificats utilisateur partagés* nécessitent un niveau supérieur de vérification de Microsoft Store. Tous les développeurs ne souhaitent pas effectuer la vérification de niveau supérieur. Activez ce paramètre uniquement si vous avez besoin d’utiliser l’authentification intégrée de Windows avec un domaine Azure AD fédéré.

## <a name="test-your-code"></a>Test de votre code

Pour tester votre application, appuyez sur F5 afin d’exécuter votre projet dans Visual Studio. Votre fenêtre principale s’affiche :

![Interface utilisateur de l’application](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Lorsque vous êtes prêt à passer aux tests, sélectionnez **Call Microsoft Graph API** (Appeler l’API Microsoft Graph). Utilisez ensuite un compte professionnel Azure AD ou un compte Microsoft (par exemple, live.com ou outlook.com) pour vous connecter. S’il s’agit de la première fois, une fenêtre vous invitant à vous connecter s’affiche :

![page de connexion](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Consentement

La première fois que vous vous connectez à votre application, un écran de consentement semblable à ce qui suit s’affiche. Sélectionnez **Oui** pour donner explicitement votre consentement pour l’accès :

![Écran de consentement d’accès](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Résultats attendus

Les informations de profil utilisateur retournées par l’appel de l’API Microsoft Graph s’affichent dans l’écran **API Call Results** (Résultats de l’appel d’API) :

![Écran API Call Results (Résultats de l’appel d’API)](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Des informations de base sur le jeton obtenu via `AcquireTokenInteractive` ou `AcquireTokenSilent` s’affichent également dans la zone **Token Info** (Informations sur le jeton) :

|Propriété  |Format  |Description |
|---------|---------|---------|
|**Nom d’utilisateur** |<span>user@domain.com</span> |Nom qui identifie l’utilisateur.|
|**Token Expires** |DateTime |L’heure d’expiration du jeton. MSAL repousse la date d’expiration en renouvelant le jeton si nécessaire.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue *user.read* pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription des applications. D’autres API de Microsoft Graph et des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. L’API Microsoft Graph nécessite l’étendue *Calendars.Read* pour répertorier les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée *Calendars.Read* aux informations d’inscription de l’application. Ajoutez ensuite l’étendue *Calendars.Read* à l’appel `acquireTokenSilent`.

> [!NOTE]
> Les utilisateurs peuvent être invités à donner des consentements supplémentaires lorsque vous augmentez le nombre d’étendues.

## <a name="known-issues"></a>Problèmes connus

### <a name="issue-1"></a>Problème 1

Vous recevez l’un des messages d’erreur suivants quand vous vous connectez à votre application dans un domaine Azure AD fédéré :

* Aucun certificat client valide trouvé dans la demande.
* Aucun certificat valide trouvé dans le magasin de certificats de l’utilisateur.
* Réessayez en choisissant une autre méthode d’authentification.

**Cause :** les fonctionnalités d’entreprise et de certificats ne sont pas activées.

**Solution :** suivez les étapes de [l’authentification intégrée sur des domaines fédérés](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problème 2

Vous activez l’[authentification intégrée sur des domaines fédérés](#enable-integrated-authentication-on-federated-domains-optional) et essayez d’utiliser Windows Hello sur un ordinateur Windows 10 pour vous connecter à un environnement où l’authentification multifacteur est configurée. La liste des certificats s’affiche. Toutefois, si vous choisissez d’utiliser votre code confidentiel, la fenêtre correspondante ne s’affiche jamais.

**Cause :** ce problème est une limitation connue du répartiteur d’authentification web dans les applications UWP qui s’exécutent sur les appareils de bureau Windows 10. Il fonctionne correctement sur Windows 10 Mobile.

**Solution de contournement :** sélectionnez **Connexion avec d’autres options**, puis **Sign in with a username and password** (Se connecter avec un nom d’utilisateur et un mot de passe). Sélectionnez **Provide your password** (Indiquer votre mot de passe), puis passez au processus d’authentification par téléphone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Aidez-nous à améliorer la plateforme d’identité Microsoft. Faites-nous part de votre avis en répondant à une petite enquête de deux questions.

> [!div class="nextstepaction"]
> [Enquête sur la plateforme d’identité Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
