---
title: Connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application de bureau .NET (WPF) | Microsoft Docs
description: Découvrez comment créer une application de bureau Windows .NET qui s’intègre à Azure AD pour la connexion et appelle des API protégées par Azure AD à l’aide d’OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 57c6b477057fb4100cff5726a4d13c6d24d80906
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695268"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Démarrage rapide : Connecter des utilisateurs et appeler l’API Microsoft Graph à partir d’une application de bureau .NET (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Pour les clients natifs .NET qui doivent accéder à des ressources protégées, Azure Active Directory (Azure AD) fournit la bibliothèque d’authentification Active Directory (ADAL). Avec la bibliothèque ADAL, votre application peut obtenir facilement des jetons d’accès. 

Dans ce guide de démarrage rapide, vous apprendrez à générer une application To Do List WPF .NET qui :

* obtient des jetons d’accès pour appeler l’API Graph Azure AD à l’aide du protocole d’authentification OAuth 2.0 ;
* recherche, dans un répertoire, d’utilisateurs correspondant à un alias donné ;
* déconnexion des utilisateurs.

Pour générer l’application fonctionnelle complète, vous devez :

1. inscrire votre application auprès d’Azure AD ;
2. installer et configurer la bibliothèque ADAL ;
3. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

## <a name="prerequisites"></a>Prérequis

Pour commencer, configurez les prérequis suivants :

* Téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).
* Vous disposez d’un locataire Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Étape 1 : Inscrire l’application DirectorySearcher

Pour que votre application puisse obtenir des jetons, vous devez tout d’abord l’inscrire dans votre locataire Azure AD et lui donner l’autorisation d’accéder à l’API Graph Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, sélectionnez votre compte et, dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.
3. Sélectionnez **Tous les services** dans le volet de navigation de gauche, puis choisissez **Azure Active Directory**.
4. Dans **Inscriptions d’applications**, choisissez **Ajouter**.
5. Suivez les invites à l’écran et créez une application cliente **native**.
    * Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    * L’ **URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer des réponses concernant les jetons. Saisissez une valeur spécifique à votre application, par exemple `http://DirectorySearcher`.

6. Une fois l’inscription terminée, AAD affecte un ID d’application unique à votre application. Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les sections suivantes.
7. Sur la page **Paramètres**, choisissez **Autorisations requises**, puis **Ajouter**. Sélectionnez **Microsoft Graph** en tant qu’API, puis ajoutez l’autorisation **Lire les données de l’annuaire** sous **Autorisations déléguées**. Grâce à cette autorisation, votre application peut rechercher des utilisateurs de l’API Graph.

## <a name="step-2-install-and-configure-adal"></a>Étape 2 : Installer et configurer la bibliothèque ADAL

Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité. Pour que la bibliothèque ADAL communique avec Azure AD, vous devez lui fournir des informations sur l’inscription de votre application.

1. Commencez par ajouter la bibliothèque ADAL au projet `DirectorySearcher` à l’aide de la console du gestionnaire de package.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. Dans le projet `DirectorySearcher`, ouvrez `app.config`.
1. Remplacez les valeurs des éléments de la section `<appSettings>` afin qu’elles reflètent les valeurs que vous avez saisies dans le Portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
  * `ida:Tenant` correspond au domaine de votre locataire Azure AD (contoso.onmicrosoft.com, par exemple).
  * `ida:ClientId` correspond à l’ID client de votre application, que vous avez copié à partir du portail.
  * `ida:RedirectUri` correspond à l’URL de redirection que vous avez inscrite dans le portail.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Étape 3 : Utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD

Le principe de base de la bibliothèque ADAL consiste simplement à appeler `authContext.AcquireTokenAsync(...)` chaque fois que votre application a besoin d’un jeton d’accès, et la bibliothèque ADAL s’occupe du reste.

1. Dans le projet `DirectorySearcher`, ouvrez `MainWindow.xaml.cs`.
1. Recherchez la méthode `MainWindow()`. 
1. Initialisez `AuthenticationContext` pour votre application (soit la classe principale de la bibliothèque ADAL). `AuthenticationContext` vous permet de fournir à la bibliothèque ADAL les coordonnées dont elle a besoin pour communiquer avec Azure AD et lui indiquer comment mettre en cache des jetons.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Recherchez la méthode `Search(...)` qui est appelée lorsque l’utilisateur clique sur le bouton **Rechercher** dans l’interface utilisateur de l’application. Cette méthode effectue une demande GET auprès de l’API Graph Azure AD pour l’interroger à propos d’utilisateurs dont l’UPN commence par le terme de recherche donné.
1. Cependant, pour interroger l’API Graph, vous devez inclure un jeton d’accès (access_token) dans l’en-tête `Authorization` de la requête ; c’est à ce moment qu’intervient la bibliothèque ADAL.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    Lorsque votre application demande un jeton en appelant `AcquireTokenAsync(...)`, la bibliothèque ADAL tente de renvoyer un jeton sans demander à l’utilisateur ses informations d’identification.
    * Si la bibliothèque ADAL détermine que l’utilisateur doit se connecter pour obtenir un jeton, elle affiche une boîte de dialogue de connexion, récupère les informations d’identification de l’utilisateur et renvoie un jeton après une authentification réussie. 
    * Si la bibliothèque ADAL ne peut pas renvoyer un jeton pour une raison quelconque, `AdalException`est renvoyé.

1. Notez que l’objet `AuthenticationResult` contient un objet `UserInfo` qui peut être utilisé pour collecter des informations dont votre application peut avoir besoin. Dans DirectorySearcher, `UserInfo` est utilisé pour personnaliser l’interface utilisateur de l’application avec l’ID de l’utilisateur.
1. Lorsque l’utilisateur sélectionne le bouton **Déconnexion**, vérifiez que l’appel suivant vers `AcquireTokenAsync(...)` demande à l’utilisateur de se connecter. Vous pouvez facilement y parvenir avec la bibliothèque ADAL, en vidant le cache du jeton :

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Si l’utilisateur ne clique pas sur le bouton **Déconnexion**, vous devez maintenir la session de l’utilisateur pour sa prochaine exécution de DirectorySearcher. Lorsque l’application démarre, vous pouvez rechercher dans le cache de jetons de la bibliothèque ADAL un jeton existant et mettre à jour l’interface utilisateur en conséquence.

1. Dans la méthode `CheckForCachedToken()`, passez un autre appel à `AcquireTokenAsync(...)`, cette fois en transmettant le paramètre `PromptBehavior.Never`. `PromptBehavior.Never` indiquera à ADAL que l’utilisateur ne doit pas être invité à se connecter et que la bibliothèque ADAL doit à la place lever une exception s’il est impossible de renvoyer un jeton.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without singing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Félicitations ! Vous disposez désormais d’une application WPF .NET fonctionnelle pouvant authentifier les utilisateurs, appeler en toute sécurité les API web à l’aide d’OAuth 2.0 et obtenir des informations de base concernant l’utilisateur. Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs. Exécutez votre application DirectorySearcher et connectez-vous avec l’un de ces utilisateurs. Recherchez d’autres utilisateurs en fonction de leur UPN. Fermez l’application et réexécutez-la. Remarquez que la session utilisateur reste identique. Déconnectez-vous et reconnectez-vous sous le nom d’un autre utilisateur.

La bibliothèque ADAL facilite l’intégration de ces fonctionnalités d’identité communes dans votre application. Elle effectue les tâches ingrates pour vous, notamment la gestion du cache, la prise en charge du protocole OAuth, la présentation d’une IU de connexion à l’utilisateur, l’actualisation des jetons expirés et bien plus encore. La seule chose que vous devez vraiment connaître est un appel unique d’API : `authContext.AcquireTokenAsync(...)`.

Pour référence, consultez l’exemple terminé (sans vos valeurs de configuration) [sur GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment protéger une API web à l’aide des jetons d’accès du porteur OAuth 2.0.
> [!div class="nextstepaction"]
> [Sécurisation d’une API web .NET avec Azure AD &gt;&gt;](quickstart-v1-dotnet-webapi.md)
