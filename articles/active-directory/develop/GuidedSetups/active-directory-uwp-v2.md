---
title: Prise en main d’Azure AD v2 UWP | Microsoft Docs
description: Cet article explique comment des applications (XAML) de plateforme Windows universelle peuvent appeler une API qui requiert des jetons d’accès d’un point de terminaison Azure Active Directory v2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763371"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Appeler l’API Microsoft Graph à partir d’une application de plateforme Windows universelle (UWP)

Ce guide montre comment une application (XAML) de plateforme Windows universelle (UWP) native peut obtenir un jeton d’accès, puis utiliser celui-ci pour appeler l’API Microsoft Graph ou d’autres API qui requièrent des jetons d’accès d’un point de terminaison Azure Active Directory v2.

À la fin de ce guide, votre application pourra appeler une API protégée à l’aide de comptes personnels (y compris outlook.com et live.com), ainsi que de comptes professionnels et scolaires de n’importe quelle société ou organisation qui possède Azure Active Directory.  

> Ce guide requiert de disposer de Visual Studio 2017 avec le Développement pour la plateforme Windows universelle. Pour obtenir des instructions sur la façon de télécharger et configurer Visual Studio afin de développer des applications de plateforme Windows universelle, voir cet [article](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "Configuration de Visual Studio pour la plateforme Windows universelle").

### <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de ce guide](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

L’exemple d’application créé dans le cadre de ce guide permet à une application UWP d’interroger l’API Microsoft Graph ou une API web qui accepte des jetons d’un point de terminaison Azure Active Directory v2. Pour ce scénario, un jeton est ajouté aux requêtes HTTP via l’en-tête d’autorisation. Les acquisitions et renouvellements de jetons sont gérés par Microsoft Authentication Library (MSAL).

### <a name="nuget-packages"></a>Packages NuGet

Ce guide utilise les packages NuGet suivants :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Bibliothèque d’authentification Microsoft (MSAL)|


## <a name="set-up-your-project"></a>Configuration de votre projet

Cette section fournit des instructions détaillées sur la manière d’intégrer une application (XAML) de bureau Windows .NET avec l’option *Se connecter avec Microsoft* afin de pouvoir interroger des API web qui requièrent un jeton, telle l’API Microsoft Graph.

L’application créée dans le cadre de ce guide affiche un bouton permettant d’interroger l’API Graph, un bouton de déconnexion, et des zones de texte qui affichent les résultats des appels.

> Vous préférez télécharger le projet Visual Studio de cet exemple ? [Téléchargez un projet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) et passez à [inscription d’application](#register-your-application "l’étape d’inscription d’application") pour configurer l’exemple de code avant de l’exécuter.


### <a name="create-your-application"></a>Créer votre application
1. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet**.<br/>
2. Sous *Modèles*, sélectionnez **Visual C#**.
3. Sélectionnez **Application vide (Windows universel)**.
4. Attribuez-lui un nom, puis cliquez sur « OK ».
5. Si vous y êtes invité, n’hésitez pas à sélectionner n’importe quelle version pour *Cible* et la version *Minimale*, puis cliquez sur « OK » :<br/><br/>![Versions minimale et cible](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Ajoutez Microsoft Authentication Library (MSAL) à votre projet
1. Dans Visual Studio, cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.
2. Copiez et collez la commande suivante dans la fenêtre Console du Gestionnaire de package :

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Le package ci-dessus installe [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL gère l’acquisition, la mise en cache et l’actualisation des jetons d’utilisateur permettant d’accéder aux API protégées par Azure Active Directory v2.

## <a name="initialize-msal"></a>Initialiser MSAL
Cette étape vous aide à créer une classe pour gérer l’interaction avec la bibliothèque MSAL, telle que pour la gestion des jetons.

1. Ouvrez le fichier **App.xaml.cs**, puis ajoutez la référence de la bibliothèque MSAL à la classe :

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ajoutez les deux lignes suivantes à la classe de l’application (à l’intérieur du bloc <code>sealed partial class App : Application</code>) :

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Créer l’interface utilisateur de votre application

Un fichier **MainPage.xaml** doit être automatiquement créé dans le cadre de votre modèle de projet. Ouvrez ce fichier, puis suivez les instructions :

1.  Remplacez le nœud **<Grid>** de votre application par :

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
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utiliser la bibliothèque d’authentification Microsoft (MSAL) afin d’obtenir un jeton pour l’API Microsoft Graph

Cette section montre comment utiliser MSAL afin d’obtenir un jeton pour l’API Microsoft Graph.

1.  Dans le fichier **MainPage.xaml.cs**, ajoutez la référence de la bibliothèque MSAL à la classe :

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Remplacez le code de votre classe <code>MainPage</code> par :

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

### <a name="more-information"></a>Informations complémentaires
#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive
L’appel de la méthode `AcquireTokenAsync` affiche une fenêtre invitant les utilisateurs à se connecter. Les applications requièrent généralement que les utilisateurs se connectent de manière interactive la première fois qu’ils cherchent à accéder à une ressource protégée. Ils peuvent également avoir besoin de se connecter en cas d’échec d’une opération en mode silencieux pour obtenir un jeton (par exemple, quand un mot de passe utilisateur a expiré).

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux
La méthode `AcquireTokenSilentAsync` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand `AcquireTokenAsync` est exécuté pour la première fois, la méthode `AcquireTokenSilentAsync` est généralement celle à utiliser pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants, étant donné que les appels pour les demandes ou renouvellements de jetons se font en mode silencieux.

La méthode `AcquireTokenSilentAsync` peut échouer. Cet échec peut être dû à une déconnexion de l’utilisateur ou à la modification de son mot de passe sur un autre appareil. Quand la bibliothèque MSAL détecte que le problème peut être résolu par une intervention interactive, elle déclenche une exception `MsalUiRequiredException`. Votre application peut gérer cette exception de deux manières :

* Elle peut appeler immédiatement `AcquireTokenAsync`. Cet appel invite l’utilisateur à se connecter. Ce modèle est normalement utilisé dans des applications en ligne où aucun contenu hors connexion n’est disponible pour l’utilisateur. L’exemple généré par cette installation guidée utilise ce modèle, que vous pouvez voir en action la première fois que vous exécutez l’exemple. 
    * Aucun utilisateur n’ayant encore utilisé l’application, `PublicClientApp.Users.FirstOrDefault()` contient une valeur null, et une exception `MsalUiRequiredException` est levée. 
    * Le code de l’exemple gère ensuite cette exception en appelant `AcquireTokenAsync`, après quoi l’utilisateur est invité à se connecter.

* Il peut également afficher à la place une indication visuelle informant les utilisateurs qu’une connexion interactive est nécessaire, pour permettre à ces derniers de sélectionner le bon moment pour se connecter. L’application peut également effectuer une nouvelle tentative de `AcquireTokenSilentAsync` ultérieurement. Ce modèle est souvent utilisé quand les utilisateurs peuvent utiliser d’autres fonctionnalités de l’application sans interruption, par exemple, quand le contenu hors connexion est disponible dans l’application. Dans ce cas, les utilisateurs peuvent décider de se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes. L’application peut également décider d’effectuer une nouvelle tentative de `AcquireTokenSilentAsync` une fois le réseau rétabli après une indisponibilité temporaire.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

1. Ajoutez la nouvelle méthode suivante à votre fichier **MainPage.xaml.cs**. Cette méthode permet d’envoyer une demande `GET` à l’API Graph à l’aide d’un en-tête d’autorisation :

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

Dans cet exemple d’application, la méthode `GetHttpContentWithToken` est utilisée pour envoyer une demande HTTP `GET` à une ressource protégée qui requiert un jeton, puis pour renvoyer le contenu à l’appelant. Cette méthode ajoute le jeton acquis dans l’*en-tête d’autorisation HTTP*. Dans cet exemple, la ressource est le point de terminaison *me* de l’API Graph de Microsoft, qui affiche les informations du profil de l’utilisateur.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Ajouter une méthode pour déconnecter l’utilisateur

1. Pour déconnecter l’utilisateur, ajoutez la méthode suivante au fichier **MainPage.xaml.cs** :

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

### <a name="more-info-on-sign-out"></a>Plus d’informations sur la déconnexion

La méthode `SignOutButton_Click` supprime l’utilisateur du cache d’utilisateur de MSAL. Cette opération revient à indiquer à MSAL d’oublier l’utilisateur actuel afin qu’une demande future d’acquisition de jeton ne puisse aboutir que si elle est effectuée de manière interactive.
Bien que l’application de cet exemple ne prenne en charge qu’un seul utilisateur, MSAL autorise les scénarios où plusieurs comptes peuvent être connectés en même temps. C’est notamment le cas dans une application de messagerie, où un utilisateur possède plusieurs comptes.

## <a name="display-basic-token-information"></a>Afficher les informations de base du jeton

1. Pour afficher les informations de base du jeton, ajoutez la méthode suivante à votre fichier **MainPage.xaml.cs** :

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

### <a name="more-information"></a>Informations complémentaires

Les jetons d’ID acquis via *OpenID Connect* contiennent également un petit sous-ensemble d’informations qui sont pertinentes pour l’utilisateur. `DisplayBasicTokenInfo` affiche les informations de base du jeton : par exemple, le nom affiché et l’ID de l’utilisateur, ainsi que la date d’expiration du jeton et la chaîne qui représente le jeton d’accès lui-même. Ces informations sont affichées. Vous pouvez cliquer sur le bouton **Call Microsoft Graph API (Appeler l’API Graph de Microsoft)** plusieurs fois et voir que le même jeton a été réutilisé pour les demandes suivantes. Vous constatez également que la date d’expiration est différée lorsque MSAL détermine qu’il est temps de renouveler le jeton.

## <a name="register-your-application"></a>Inscrivez votre application

Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :
1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application. 
3. Assurez-vous que la case de l’option Guided Setup (Installation guidée) est désactivée.
4. Cliquez sur **Ajouter des plateformes**, sélectionnez **Application native**, puis choisissez Enregistrer.
5. Copiez le GUID dans ID d’application, revenez à Visual Studio, ouvrez **App.xaml.cs**, puis remplacez `your_client_id_here` par l’ID d’application que vous venez d’inscrire :

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Activer l’authentification intégrée sur des domaines fédérés (facultatif)

Pour activer l’Authentification intégrée de Windows en cas d’utilisation avec un domaine Azure Active Directory fédéré, le manifeste de l’application doit activer des fonctionnalités supplémentaires :

1. Double-cliquez sur **Package.appxmanifest**.
2. Sélectionnez l’onglet **Capacités**, puis assurez-vous que les paramètres suivants sont activés :

    - Authentification en entreprise
    - Réseaux privés (client et serveur)
    - Certificats utilisateur partagés 

3. Ensuite, ouvrez le fichier **App.xaml.cs**, puis ajoutez la ligne suivante dans le constructeur App :

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> L’Authentification intégrée de Windows n’est pas configurée par défaut pour cet exemple parce que les applications demandant les capacités *Authentification entreprise* ou *Certificats utilisateur partagés* requièrent un niveau supérieur de vérification par le Windows Store, et que certains développeurs ne souhaitent pas utiliser le niveau supérieur de vérification. N’activez ce paramètre que si vous avez besoin de l’Authentification intégrée de Windows avec un domaine Azure Active Directory fédéré.


## <a name="test-your-code"></a>Test de votre code

Pour tester votre application, appuyez sur `F5` afin d’exécuter votre projet dans Visual Studio. Votre fenêtre principale doit alors s’afficher :

![Interface utilisateur de l’application](media/active-directory-uwp-v2.md/testapp-ui.png)

Lorsque vous êtes prêt pour le test, cliquez sur *Call Microsoft Graph API* (Appeler l’API Microsoft Graph) et utilisez un compte Microsoft Azure Active Directory (compte de société) ou un compte Microsoft (live.com, outlook.com) pour vous connecter. S’il s’agit de la première fois, une fenêtre vous invitant à vous connecter s’affiche :

![page de connexion](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Consentement
La première fois que vous vous connectez à votre application, un écran de consentement semblable à celui-ci, dans lequel vous devez accepter explicitement ce qui est indiqué, s’affiche :

![Écran de consentement](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Résultats attendus
Les informations de profil utilisateur renvoyées par l’appel de l’API Microsoft Graph doivent s’afficher dans l’écran API Call Results (Résultats de l’appel d’API) :

![Écran des résultats](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Des informations de base sur le jeton obtenu via `AcquireTokenAsync` ou `AcquireTokenSilentAsync` doivent également s’afficher dans la zone Token Info (Informations sur le jeton) :

|Propriété  |Format  |Description |
|---------|---------|---------|
|**Name** |Nom complet de l’utilisateur |Prénom et nom de l’utilisateur|
|**Nom d’utilisateur** |<span>user@domain.com</span> |Nom d’utilisateur employé pour identifier l’utilisateur.|
|**Token Expires** |Datetime |Date et heure auxquelles expire le jeton. MSAL repousse la date d’expiration en renouvelant le jeton si nécessaire.|
|**Access Token** |Chaîne |Chaîne de jeton qui est envoyée aux requêtes HTTP qui nécessitent un *en-tête d’autorisation*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Voir le contenu du jeton d’accès (facultatif)
Si vous le souhaitez, vous pouvez copier la valeur figurant dans le jeton d’accès et la coller dans https://jwt.ms afin de la décoder et de voir la liste des revendications.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue *user.read* pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le portail d’inscription de l’application. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. L’API Microsoft Graph nécessite l’étendue *Calendars.Read* pour répertorier les calendriers de l’utilisateur.

Pour accéder aux calendriers de l’utilisateur dans le contexte d’une application, ajoutez l’autorisation déléguée *Calendars.Read* aux informations d’inscription de l’application. Ajoutez ensuite l’étendue *Calendars.Read* à l’appel `acquireTokenSilent`. 

> [!NOTE]
> L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

## <a name="known-issues"></a>Problèmes connus

### <a name="issue-1"></a>Problème 1 :
Il se peut que vous receviez l’une des erreurs suivantes lors de l’inscription de votre application sur un domaine Active Directory Azure fédéré :
 - Aucun certificat client valide trouvé dans la demande.
 - Aucun certificat valide trouvé dans le magasin de certificats de l’utilisateur.
 - Réessayez en choisissant une autre méthode d’authentification.

**Cause :** les capacités Entreprise et Certificats ne sont pas activées.

**Solution :** suivez les étapes de l’[authentification intégrée sur des domaines fédérés](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problème2 :
Après que vous avez activé l’[authentification intégrée sur des domaines fédérés](#enable-integrated-authentication-on-federated-domains-optional) et essayé d’utiliser Windows Hello sur un ordinateur Windows 10 pour inscrire un environnement avec une authentification multifacteur configurée, la liste des certificats est présentée. En revanche, si vous choisissez d’utiliser votre code confidentiel, la fenêtre de code confidentiel ne s’affiche jamais.

**Cause :** limitation connue du répartiteur d’authentification web dans les applications UWP s’exécutant sur Windows 10 Desktop (fonctionne correctement sur Windows 10 Mobile).

**Solution de contournement :** les utilisateurs doivent opter pour d’autres options de connexion, choisir de *se connecter avec un nom d’utilisateur et un mot de passe*, sélectionner le mot de passe, puis passer à l’authentification par téléphone.

