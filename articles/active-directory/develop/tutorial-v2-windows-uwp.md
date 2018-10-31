---
title: Prise en main d’Azure AD v2.0 UWP | Microsoft Docs
description: Cet article explique comment des applications de plateforme Windows universelle (UWP) peuvent appeler une API qui requiert des jetons d’accès du point de terminaison Azure Active Directory v2.0.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4ba4e844ed6bb01204b7a0adf5020aec255147dd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986540"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Appeler l’API Microsoft Graph à partir d’une application de plateforme Windows universelle (XAML)

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ce guide explique comment une application de plateforme Windows universelle (UWP) native peut demander un jeton d’accès, puis appeler l’API Microsoft Graph. Ce guide s’applique également aux autres API qui nécessitent des jetons d’accès du point de terminaison Azure Active Directory v2.0.

À la fin de ce guide, votre application appelle une API protégée à l’aide de comptes personnels. Outlook.com, live.com, etc. en sont des exemples. Votre application appelle également des comptes professionnels et scolaires de toute entreprise ou organisation possédant Azure Active Directory.

>[!NOTE]
> Ce guide requiert de disposer de Visual Studio 2017 avec le Développement pour la plateforme Windows universelle. Pour obtenir des instructions sur le téléchargement et la configuration de Visual Studio afin de développer des applications de plateforme Windows universelle, consultez l’article [Préparation](https://docs.microsoft.com/windows/uwp/get-started/get-set-up).

## <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Graphique de fonctionnement de ce guide](./media/tutorial-v2-windows-uwp/uwp-intro.png)

Ce guide crée un exemple d’application UWP qui interroge l’API Microsoft Graph ou une API web qui accepte des jetons du point de terminaison Azure Active Directory v2.0. Pour ce scénario, un jeton est ajouté aux requêtes HTTP via l’en-tête d’autorisation. Microsoft Authentication Library (MSAL) gère les acquisitions et renouvellements de jetons.

## <a name="nuget-packages"></a>Packages NuGet

Ce guide utilise les packages NuGet suivants :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Configuration de votre projet

Cette section fournit des instructions détaillées pour intégrer une application .NET de bureau Windows (XAML) avec *Sign-In with Microsoft* (Se connecter avec Microsoft). Elle peut ensuite interroger les API web qui requièrent un jeton, par exemple l’API Microsoft Graph.

Avec ce guide, vous allez créer une application qui affiche un bouton permettant d’interroger l’API Graph, un bouton de déconnexion, et des zones de texte qui affichent les résultats des appels.

> [!NOTE]
> Préférez-vous télécharger le projet Visual Studio de cet exemple ? [Téléchargez un projet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) et passez à l’étape [d’inscription d’application](#register-your-application "l’étape d’inscription d’application") pour configurer l’exemple de code avant son exécution.


### <a name="create-your-application"></a>Créer votre application

1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.
2. Sous **Modèles**, sélectionnez **Visual C#**.
3. Sélectionnez **Application vide (Universal Windows)**.
4. Nommez l’application, puis sélectionnez **OK**.
5. Si vous y êtes invité, sélectionnez une version pour les versions **Cible** et **Minimale**, puis sélectionnez **OK**.

    >![Versions minimale et cible](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Ajouter Microsoft Authentication Library à votre projet
1. Dans Visual Studio, sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**.
2. Copiez et collez la commande suivante dans la fenêtre **Console du Gestionnaire de package** :

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre -Version 1.1.4-preview0002
    ```

> [!NOTE]
> Cette commande installe [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL acquiert, met en cache et actualise les jetons utilisateur qui accèdent aux API protégées par Azure Active Directory v2.0.

> [!NOTE]
> Ce didacticiel n’utilise pas encore la dernière version de MSAL.NET, mais nous travaillons à sa mise à jour.

## <a name="initialize-msal"></a>Initialiser MSAL
Cette étape vous aide à créer une classe pour gérer l’interaction avec MSAL, telle que la gestion des jetons.

1. Ouvrez le fichier **App.xaml.cs** et ajoutez la référence de MSAL à la classe :

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ajoutez les deux lignes suivantes à la classe de l’application (à l’intérieur du bloc <code>sealed partial class App : Application</code>) :

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

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
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }
    
            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

### <a name="more-information"></a>Plus d’informations

#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive

L’appel de la méthode `AcquireTokenAsync` affiche une fenêtre invitant les utilisateurs à se connecter. Les applications requièrent généralement que les utilisateurs se connectent de manière interactive la première fois qu’ils cherchent à accéder à une ressource protégée. Ils peuvent également avoir besoin de se connecter en cas d’échec d’une opération en mode silencieux pour obtenir un jeton, par exemple lorsque le mot de passe d’un utilisateur est arrivé à expiration.

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux

La méthode `AcquireTokenSilentAsync` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Après que la méthode `AcquireTokenAsync` a été exécutée pour la première fois et que l’utilisateur a été invité à entrer les informations d’identification, il convient d’utiliser la méthode `AcquireTokenSilentAsync` pour demander des jetons pour les appels suivants, car elle permet d’acquérir des jetons en mode silencieux. MSAL gère la mise en cache et le renouvellement des jetons.

La méthode `AcquireTokenSilentAsync` peut échouer. Cet échec peut être dû à une déconnexion de l’utilisateur ou à la modification de son mot de passe sur un autre appareil. Quand la bibliothèque MSAL détecte que le problème peut être résolu par une intervention interactive, elle déclenche une exception `MsalUiRequiredException`. Votre application peut gérer cette exception de deux manières :

* Elle peut appeler immédiatement `AcquireTokenAsync`. Cet appel invite l’utilisateur à se connecter. Normalement, ce modèle est utilisé dans des applications en ligne où aucun contenu hors connexion n’est disponible pour l’utilisateur. L’exemple créé avec cette installation guidée suit ce modèle. Vous le découvrez en action lors de la première exécution de l’exemple. 
    * Aucun utilisateur n’ayant encore utilisé l’application, `PublicClientApp.Users.FirstOrDefault()` contient une valeur null, et une exception `MsalUiRequiredException` est levée.
    * Le code contenu dans l’exemple gère alors l’exception en appelant `AcquireTokenAsync`. Cet appel invite l’utilisateur à se connecter.

* Elle peut également présenter une indication visuelle aux utilisateurs montrant qu’une connexion interactive est requise. Ils peuvent alors sélectionner le moment opportun pour se connecter. L’application peut également effectuer une nouvelle tentative de `AcquireTokenSilentAsync` ultérieurement. Ce modèle est souvent utilisé lorsque les utilisateurs peuvent avoir recours à d’autres fonctionnalités de l’application sans interruption de service, par exemple lorsque le contenu hors connexion est disponible dans l’application. Dans ce cas, les utilisateurs peuvent décider de se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes. L’application peut également décider d’effectuer une nouvelle tentative de `AcquireTokenSilentAsync` une fois le réseau restauré après une indisponibilité temporaire.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

* Ajoutez la nouvelle méthode suivante à au fichier **MainPage.xaml.cs**. Cette méthode permet d’envoyer une demande `GET` à l’API Graph à l’aide d’un en-tête [Authorize] :

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
            request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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
    private void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        if (App.PublicClientApp.Users.Any())
        {
            try
            {
                App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
                this.ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    }
    ```

### <a name="more-information-on-sign-out"></a>En savoir plus sur la déconnexion

La méthode `SignOutButton_Click` supprime l’utilisateur du cache d’utilisateur MSAL. Cette méthode permet d’indiquer efficacement à MSAL d’oublier l’utilisateur actuel. Une demande ultérieure d’acquisition d’un jeton aboutit uniquement si elle est interactive.
L’application de cet exemple prend en charge un seul utilisateur, mais MSAL prend en charge des scénarios dans lesquels plusieurs comptes peuvent être connectés en même temps. C’est le cas, par exemple, d’une application de messagerie hébergeant plusieurs comptes d’un même utilisateur.

## <a name="display-basic-token-information"></a>Afficher les informations de base du jeton

* Pour afficher les informations de base du jeton, ajoutez la méthode suivante au fichier **MainPage.xaml.cs** :

    ```csharp
    /// <summary>
    /// Display basic information contained in the token
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
            TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
            TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Plus d’informations

Les jetons d’ID acquis via **OpenID Connect** contiennent également un petit sous-ensemble d’informations qui sont pertinentes pour l’utilisateur. `DisplayBasicTokenInfo` affiche les informations de base contenues dans le jeton, par exemple, le nom d’affichage et l’ID de l’utilisateur, la date d’expiration du jeton et la chaîne qui représente le jeton d’accès proprement dit. Si vous cliquez plusieurs fois sur le bouton **Call Microsoft Graph API** (Appeler l’API Microsoft Graph), vous observerez que le même jeton a été réutilisé pour les demandes suivantes. Vous observez également la date d’expiration différée lorsque MSAL détermine qu’il est temps de renouveler le jeton.

## <a name="register-your-application"></a>Inscrivez votre application

Maintenant, vous devez inscrire votre application dans le portail d’inscription des applications de Microsoft :
1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application.
3. Assurez-vous que l’option **Guided Setup** (Installation guidée) *n’est pas sélectionnée*.
4. Sélectionnez successivement **Ajouter des plateformes**, **Application native** et **Enregistrer**.
5. Copiez le GUID dans le champ **ID d’application**, revenez à Visual Studio, ouvrez le fichier **App.xaml.cs**, puis remplacez `your_client_id_here` par l’ID d’application que vous venez d’inscrire :

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Activer l’authentification intégrée sur des domaines fédérés (facultatif)

Pour activer l’Authentification intégrée de Windows en cas d’utilisation avec un domaine Azure Active Directory fédéré, le manifeste de l’application doit activer des fonctionnalités supplémentaires :

1. Double-cliquez sur **Package.appxmanifest**.
2. Sélectionnez l’onglet **Capacités**, puis assurez-vous que les paramètres suivants sont activés :

    - Authentification en entreprise
    - Réseaux privés (client et serveur)
    - Certificats utilisateur partagés

3. Ouvrez le fichier **App.xaml.cs**, puis ajoutez la ligne suivante dans le constructeur App :

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> L’Authentification intégrée de Windows n’est pas configurée par défaut pour cet exemple. Les applications qui demandent les fonctionnalités *Authentification en entreprise* ou *Certificats utilisateur partagés* nécessitent un niveau supérieur de vérification de Microsoft Store. Tous les développeurs ne souhaitent pas effectuer la vérification de niveau supérieur. N’activez ce paramètre que si vous avez besoin de l’Authentification intégrée de Windows avec un domaine Azure Active Directory fédéré.

## <a name="test-your-code"></a>Test de votre code

Pour tester votre application, appuyez sur F5 afin d’exécuter votre projet dans Visual Studio. Votre fenêtre principale s’affiche :

![Interface utilisateur de l’application](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Lorsque vous êtes prêt à passer aux tests, sélectionnez **Call Microsoft Graph API** (Appeler l’API Microsoft Graph). Utilisez un compte de société Microsoft Azure Active Directory ou un compte Microsoft, par exemple live.com ou outlook.com pour vous connecter. S’il s’agit de la première fois, une fenêtre vous invitant à vous connecter s’affiche :

![page de connexion](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Consentement
La première fois que vous vous connectez à votre application, un écran de consentement semblable à ce qui suit s’affiche. Sélectionnez **Oui** pour donner explicitement votre consentement pour l’accès :

![Écran de consentement d’accès](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>Résultats attendus
Les informations de profil utilisateur retournées par l’appel de l’API Microsoft Graph s’affichent dans l’écran **API Call Results** (Résultats de l’appel d’API) :

![Écran API Call Results (Résultats de l’appel d’API)](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Des informations de base sur le jeton obtenu via `AcquireTokenAsync` ou `AcquireTokenSilentAsync` s’affichent également dans la zone **Token Info** (Informations sur le jeton) :

|Propriété  |Format  |Description |
|---------|---------|---------|
|**Name** |Nom complet de l’utilisateur|Prénom et nom de l’utilisateur|
|**Nom d’utilisateur** |<span>user@domain.com</span> |Nom qui identifie l’utilisateur.|
|**Token Expires** |Datetime |L’heure d’expiration du jeton. MSAL repousse la date d’expiration en renouvelant le jeton si nécessaire.|
|**Access Token** |Chaîne |Chaîne de jeton qui est envoyée aux requêtes HTTP qui nécessitent un *en-tête d’autorisation*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Voir le contenu du jeton d’accès (facultatif)
Si vous le souhaitez, copiez la valeur figurant dans la zone **Jeton d’accès** et collez-la dans https://jwt.ms afin de la décoder et de voir la liste des revendications.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue *user.read* pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription des applications. D’autres API de Microsoft Graph et des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. L’API Microsoft Graph nécessite l’étendue *Calendars.Read* pour répertorier les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée *Calendars.Read* aux informations d’inscription de l’application. Ajoutez ensuite l’étendue *Calendars.Read* à l’appel `acquireTokenSilent`.

> [!NOTE]
> Les utilisateurs peuvent être invités à donner des consentements supplémentaires lorsque vous augmentez le nombre d’étendues.

## <a name="known-issues"></a>Problèmes connus

### <a name="issue-1"></a>Problème 1
Vous recevez l’un des messages d’erreur suivants lorsque vous vous connectez à votre application dans un domaine Azure Active Directory fédéré :
 - Aucun certificat client valide trouvé dans la demande.
 - Aucun certificat valide trouvé dans le magasin de certificats de l’utilisateur.
 - Réessayez en choisissant une autre méthode d’authentification.

**Cause :** les fonctionnalités d’entreprise et de certificats ne sont pas activées.

**Solution :** suivez les étapes de [l’authentification intégrée sur des domaines fédérés](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problème 2
Vous activez [l’authentification intégrée sur des domaines fédérés](#enable-integrated-authentication-on-federated-domains-optional) et essayez d’utiliser Windows Hello sur un ordinateur Windows 10 pour vous connecter à un environnement dans lequel l’authentification multifacteur est configurée. La liste des certificats s’affiche. Toutefois, si vous choisissez d’utiliser votre code confidentiel, la fenêtre correspondante ne s’affiche jamais.

**Cause :** ce problème est une limitation connue du service Broker d’authentification web dans les applications UWP qui s’exécutent sur Windows 10 Desktop. Il fonctionne correctement sur Windows 10 Mobile.

**Solution de contournement :** sélectionnez **Connexion avec d’autres options**, puis **Sign in with a username and password** (Se connecter avec un nom d’utilisateur et un mot de passe). Sélectionnez **Provide your password** (Indiquer votre mot de passe), puis passez au processus d’authentification par téléphone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]