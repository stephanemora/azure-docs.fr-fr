---
title: Appeler une API web ASP.NET protégée par Azure AD – Plateforme d’identités Microsoft
description: Ce démarrage rapide explique comment appeler une API web ASP.NET protégée par Azure Active Directory à partir d’une application de bureau Windows (WPF). Le client WPF authentifie un utilisateur, demande un jeton d’accès et appelle l’API web.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328548"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Créer un démon multilocataire avec le point de terminaison de la plateforme d’identités Microsoft

Ce tutoriel vous montre comment utiliser la plateforme d’identités Microsoft pour accéder aux données de clients professionnels Microsoft dans un processus non interactif de longue durée. L’exemple de démon utilise l’[octroi d’informations d’identification du client OAuth2](v2-oauth2-client-creds-grant-flow.md) pour acquérir un jeton d’accès dont il se sert ensuite pour appeler [Microsoft Graph](https://graph.microsoft.io) et accéder aux données de l’organisation.

L’application est créée en tant qu’application ASP.NET MVC et elle utilise le middleware OWIN OpenID Connect pour connecter les utilisateurs.  Son composant « démon » dans cet exemple est un contrôleur d’API qui, lorsqu’il est appelé, tire (pull) une liste d’utilisateurs dans le locataire Azure AD du client à partir de Microsoft Graph.  Ce contrôleur `SyncController.cs` est déclenché par un appel AJAX dans l’application web et il utilise la [bibliothèque d’authentification Microsoft (MSAL) pour .NET](msal-overview.md) afin d’acquérir un jeton d’accès pour Microsoft Graph.

Pour obtenir un exemple d’application de démon de console plus simple, consultez le [Guide de démarrage rapide du démon .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Scénario

L’application est une application multilocataire qui sera utilisée par les clients professionnels Microsoft. Elle doit donc fournir à ces clients un moyen de la « connecter » à leurs données d’entreprise.  Durant le processus de connexion, un administrateur d’entreprise accorde d’abord des **autorisations d’application** directement à l’application afin que celle-ci puisse accéder aux données d’entreprise de manière non interactive, sans nécessiter la présence d’un utilisateur connecté.  Dans cet exemple, la majeure partie de la logique montre comment réaliser ce processus de connexion à l’aide du point de terminaison de [consentement administrateur](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) de la plateforme d’identités.

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Pour plus d’informations sur les concepts utilisés dans cet exemple, lisez la [documentation relative au flux d’informations d’identification du client de point de terminaison de la plateforme d’identités](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Prérequis

Pour exécuter l’exemple dans ce guide de démarrage rapide, vous avez besoin des éléments suivants :

- [Visual Studio 2017 ou 2019](https://visualstudio.microsoft.com/downloads/)
- Un locataire Azure Active Directory (Azure AD). Pour savoir comment obtenir un locataire Azure AD, consultez [Obtention d’un locataire Azure AD](quickstart-create-new-tenant.md)
- Un ou plusieurs comptes d’utilisateur dans votre locataire Azure AD. Cet exemple ne fonctionne pas avec les comptes Microsoft (anciennement comptes Windows Live). Par conséquent, si vous vous êtes connecté au [portail Azure](https://portal.azure.com) avec un compte Microsoft et que vous n’avez encore jamais créé de compte d’utilisateur dans votre annuaire, vous devez en créer un maintenant.

## <a name="clone-or-download-this-repository"></a>Cloner ou télécharger ce dépôt

À partir de votre interpréteur de commandes ou de votre ligne de commande :

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ou [téléchargez l’exemple dans un fichier zip](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>Inscrire l’exemple d’application auprès de votre locataire Azure AD

Cet exemple contient un seul projet. Pour l’inscrire, choisissez l’une des méthodes suivantes :

- Effectuez les étapes décrites dans [Inscrire l’exemple d’application auprès de votre locataire Azure AD](#register-the-sample-application-with-your-azure-ad-tenant) et [Configurer l’exemple pour utiliser votre locataire Azure AD](#choose-the-azure-ad-tenant-for-the-applications)
- Ou utilisez des scripts PowerShell pour :
  - Créer **automatiquement** les applications Azure AD et les objets associés (mots de passe, autorisations et dépendances)
  - Modifier les fichiers de configuration des projets Visual Studio.

Si vous souhaitez utiliser la méthode automatisée :

1. Sur Windows, exécutez PowerShell et accédez à la racine du répertoire cloné.
1. Dans PowerShell, exécutez :

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Exécutez le script pour créer votre application Azure AD et configurer le code de l’exemple d’application en conséquence.
1. Dans PowerShell, exécutez :

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > D’autres méthodes d’exécution des scripts sont décrites dans les [scripts de création d’application](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Ouvrez la solution Visual Studio et cliquez sur Démarrer pour exécuter le code.

Si vous ne souhaitez pas utiliser cette méthode automatisée, effectuez les étapes ci-dessous.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>Choisir le locataire Azure AD pour les applications

Commencez par cette étape :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte est présent dans plusieurs locataires Azure AD, sélectionnez votre profil en haut à droite dans le menu en haut de la page, puis **changer d’annuaire**.
   Modifiez votre session de portail en la définissant sur le client Azure AD souhaité.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Inscrire l’application cliente (dotnet-web-daemon-v2)

1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `dotnet-web-daemon-v2`.
   - Dans la section **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel**.
   - Dans la section URI de redirection (facultatif), sélectionnez **Web** dans la zone de liste modifiable et entrez les URI de redirection suivants :
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` S’il y a plusieurs URI de redirection, vous devrez les ajouter à partir de l’onglet **Authentification** une fois que l’application aura été créée.
1. Sélectionnez **Inscrire** pour créer l’application.
1. Dans la page **Vue d’ensemble** de l’application, recherchez la valeur **ID d’application (client)** et notez-la. Vous en aurez besoin pour configurer le fichier de configuration Visual Studio pour ce projet.
1. Dans la liste des pages de l’application, sélectionnez **Authentification**.
   - Dans la section **Paramètres avancés**, affectez à **URL de déconnexion** la valeur `https://localhost:44316/Account/EndSession`.
   - Dans la section **Paramètres avancés** | **Octroi implicite**, cochez **Jetons d’accès** et **Jetons d’ID**, car cet exemple nécessite l’activation du [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md) pour permettre la connexion de l’utilisateur et l’appel d’une API.
1. Sélectionnez **Enregistrer**.
1. Dans la page **Certificats et secrets**, accédez à la section **Secrets client** et choisissez **Nouveau secret client** :

   - Tapez une description de la clé (pour le `app secret` de l’instance).
   - Sélectionnez une durée pour la clé : **Dans 1 an**, **Dans 2 ans** ou **N’expire jamais**.
   - Quand vous appuyez sur le bouton **Ajouter**, la valeur de la clé s’affiche ; copiez et enregistrez cette valeur à un emplacement sûr.
   - Vous aurez besoin de cette clé plus tard pour configurer le projet dans Visual Studio. Cette valeur de clé ne sera plus jamais affichée et aucun autre moyen ne permettra de la récupérer. Par conséquent, enregistrez-la dès qu’elle apparaît sur le portail Azure.
1. Dans la liste des pages de l’application, sélectionnez **Autorisations de l’API**
   - Cliquez sur le bouton **Ajouter une autorisation**.
   - Vérifiez ensuite que l’onglet **API Microsoft** est sélectionné.
   - Dans la section *API Microsoft couramment utilisées*, cliquez sur **Microsoft Graph**
   - Dans la section **Autorisations d’application**, vérifiez que les autorisations appropriées sont cochées : **User.Read.All**
   - Sélectionnez le bouton **Ajouter des autorisations**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configurer l’exemple pour utiliser votre locataire Azure AD

Dans les étapes ci-dessous, « ClientID » équivaut à « ID d’application » ou « AppId ».

Ouvrez la solution dans Visual Studio pour configurer les projets

### <a name="configure-the-client-project"></a>Configurer le projet client

Si vous avez utilisé les scripts d’installation, les modifications suivantes ont été automatiquement appliquées.

1. Ouvrez le fichier `UserSync\Web.Config`.
1. Recherchez la valeur `ida:ClientId` de la clé d’application et remplacez la valeur existante par l’ID d’application (clientId) de l’application `dotnet-web-daemon-v2` que vous avez copié à partir du portail Azure.
1. Recherchez la valeur `ida:ClientSecret` de la clé d’application et remplacez la valeur existante par la clé que vous avez enregistrée lors de la création de l’application `dotnet-web-daemon-v2` dans le portail Azure.

## <a name="run-the-sample"></a>Exécution de l'exemple

Nettoyez la solution et regénérez-la, exécutez l’application UserSync, puis connectez-vous en tant qu’administrateur dans votre locataire Azure AD.  Si vous n’avez pas de locataire Azure AD dédié aux tests, vous pouvez [suivre ces instructions](quickstart-create-new-tenant.md) pour en obtenir un.

Quand vous vous connectez, l’application demande d’abord votre autorisation pour vous connecter et lire votre profil utilisateur.  Ce consentement permet à l’application de s’assurer que vous êtes un utilisateur professionnel.

![Consentement de l’utilisateur](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

L’application essaie ensuite de synchroniser une liste d’utilisateurs à partir de votre locataire Azure AD, par le biais de Microsoft Graph.  Si elle n’y parvient pas, elle vous demande (en tant qu’administrateur du locataire) de connecter votre locataire à l’application.

L’application vous demande ensuite l’autorisation de lire la liste des utilisateurs dans votre locataire.

![Consentement de l’administrateur](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Vous serez déconnecté de l’application après l’octroi de l’autorisation**. Cela garantit que les jetons d’accès existants pour Graph seront supprimés du cache de jetons. Après votre reconnexion, le nouveau jeton obtenu aura les autorisations nécessaires pour effectuer des appels à Microsoft Graph.
Une fois que vous avez accordé l’autorisation, l’application est en mesure de rechercher les utilisateurs à tout moment.  Vous pouvez le vérifier en cliquant sur le bouton **Synchroniser les utilisateurs** dans la page Utilisateurs, puis en actualisant la liste des utilisateurs.  Essayez d’ajouter ou de supprimer un utilisateur, puis de resynchroniser la liste (notez toutefois que seule la première page des utilisateurs sera synchronisée).

## <a name="about-the-code"></a>À propos du code

Le code correspondant à cet exemple se trouve dans les fichiers suivants :

- Connexion initiale : `App_Start\Startup.Auth.cs`, `Controllers\AccountController.cs`.  En particulier, les actions sur le contrôleur ont un attribut Authorize qui force l’utilisateur à se connecter. L’application utilise le [flux de code d’autorisation](v2-oauth2-auth-code-flow.md) pour connecter l’utilisateur.
- Synchronisation de la liste des utilisateurs avec le magasin local en mémoire : `Controllers\SyncController.cs`
- Affichage de la liste des utilisateurs à partir du magasin local en mémoire : `Controllers\UserController.cs`
- Obtention des autorisations de l’administrateur du locataire à l’aide du point de terminaison de consentement de l’administrateur : `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>Recréer cet exemple d’application

1. Dans Visual Studio, créez un autre projet `Visual C#` `ASP.NET Web Application (.NET Framework)`. Dans l’écran suivant, choisissez le modèle de projet `MVC`. Ensuite, ajoutez des références de dossier et de base pour `Web API` car vous ajouterez un contrôleur d’API Web plus tard.  Conservez le mode d’authentification sélectionné par défaut pour le projet, à savoir `No Authentication`.
2. Sélectionnez le projet dans la fenêtre **Explorateur de solutions** et appuyez sur la touche **F4** pour afficher les propriétés du projet. Dans les propriétés du projet, définissez **SSL activé** sur `True`. Notez l’**URL SSL**. Vous devrez l’indiquer au moment de la configuration de l’inscription de cette application dans le portail Azure.
3. Ajoutez les packages NuGet suivants pour le middleware OWIN ASP.Net : `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` et `Microsoft.Owin.Host.SystemWeb` ; `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` et `Microsoft.Identity.Client`. 
4. Dans le dossier `App_Start`, créez une classe `Startup.Auth.cs`. Supprimez `.App_Start` du nom de l’espace de noms.  Remplacez le code de la classe `Startup` par le code de l’exemple d’application figurant dans le même fichier.  Veillez à copier la définition de classe entière !  La définition `public class Startup` change en `public partial class Startup`
5. Dans `Startup.Auth.cs`, corrigez les références manquantes en ajoutant les instructions `using` suggérées par la fonctionnalité IntelliSense de Visual Studio.
6. Cliquez avec le bouton droit sur le projet, sélectionnez Ajouter, sélectionnez « Classe », puis entrez « OWIN » dans la zone de recherche.  « Classe de démarrage OWIN » s’affiche en tant que sélection ; sélectionnez-la, puis nommez la classe `Startup.cs`.
7. Dans `Startup.cs`, remplacez le code de la classe `Startup` par le code de l’exemple d’application figurant dans le même fichier.  Notez de nouveau que la définition `public class Startup` change en `public partial class Startup`.
8. Dans le dossier, ajoutez une nouvelle classe appelée `MsGraphUser.cs`.  Remplacez l’implémentation par le contenu du fichier du même nom que celui de l’exemple.
9. Ajoutez un nouveau **Contrôleur MVC 5 - Vide** appelé `AccountController`. Remplacez l’implémentation par le contenu du fichier du même nom que celui de l’exemple.
10. Ajoutez un nouveau **Contrôleur MVC 5 - Vide** appelé `UserController`. Remplacez l’implémentation par le contenu du fichier du même nom que celui de l’exemple.
11. Ajoutez un nouveau **Contrôleur Web API 2 - Vide** appelé `SyncController`. Remplacez l’implémentation par le contenu du fichier du même nom que celui de l’exemple.
12. Pour l’interface utilisateur, dans le dossier `Views\Account`, ajoutez trois **Vues vides (sans modèle)** nommées `GrantPermissions`, `Index` et `UserMismatch` ; dans le dossier `Views\User`, ajoutez une vue nommée `Index`. Remplacez l’implémentation par le contenu du fichier du même nom que celui de l’exemple.
13. Mettez à jour `Shared\_Layout.cshtml` et `Home\Index.cshtml` pour lier correctement les différentes vues entre elles.

## <a name="deploy-this-sample-to-azure"></a>Déployer cet exemple sur Azure

Ce projet comporte des projets API Web/WebApp. Pour les déployer sur des sites web Azure, vous devez, pour chacun d’eux, effectuer les opérations suivantes :

- créer un site web Azure,
- publier les projets API Web/WebApp sur le site web, et
- mettre à jour son ou ses clients pour appeler le site web au lieu d’IIS Express.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>Créer et publier `dotnet-web-daemon-v2` sur un site web Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Cliquez sur `Create a resource` dans le coin supérieur gauche, sélectionnez **Web** --> **Application web** et donnez un nom à votre site web, par exemple `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Ensuite, sélectionnez les attributs `Subscription`, `Resource Group` et `App service plan and Location`. `OS` doit être défini sur **Windows** et `Publish` sur **Code**.
1. Cliquez sur `Create` et attendez la fin de la création du service d’application.
1. Quand vous recevez la notification `Deployment succeeded`, cliquez sur `Go to resource` pour accéder au nouveau service d’application.
1. Une fois le site web créé, recherchez-le dans le **tableau de bord**, puis cliquez dessus pour ouvrir l’écran **Vue d’ensemble** **d’App Services**.
1. À partir de l’onglet **Vue d’ensemble** d’App Service, téléchargez le profil de publication en cliquant sur le lien **Obtenir le profil de publication**, puis enregistrez-le.  D’autres mécanismes de déploiement, comme le contrôle de code source, peuvent également être utilisés.
1. Basculez vers Visual Studio et accédez au projet dotnet-web-daemon-v2.  Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Publier**.  Cliquez sur **Importer le profil** dans la barre inférieure, puis importez le profil de publication que vous avez téléchargé précédemment.
1. Cliquez sur **Configurer** puis, sous `Connection tab`, modifiez l’URL de destination afin que l’URL de la page d’accueil soit une URL `https` ; par exemple, [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Cliquez sur **Suivant**.
1. Sous l’onglet Paramètres, vérifiez que `Enable Organizational Authentication` n’est PAS sélectionné.  Cliquez sur **Enregistrer**. Cliquez sur **Publier** dans l’écran principal.
1. Visual Studio publie le projet et ouvre automatiquement l’URL du projet dans un navigateur.  Si la page web par défaut du projet s’affiche, la publication a réussi.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Mettre à jour l’inscription de l’application locataire Azure AD pour `dotnet-web-daemon-v2`

1. Revenez au [portail Azure](https://portal.azure.com).
Dans le volet de navigation de gauche, sélectionnez le service **Azure Active Directory**, puis sélectionnez **Inscriptions d’applications**.
1. Dans l’écran qui s’affiche, sélectionnez l’application `dotnet-web-daemon-v2`.
1. Dans la page **Authentification** | pour votre application, mettez à jour les champs URL de déconnexion avec l’adresse de votre service, par exemple [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. Dans le menu *Personnalisation*, mettez à jour l’**URL de la page d’accueil** avec l’adresse de votre service ; par exemple, [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Enregistrez la configuration.
1. Ajoutez la même URL à la liste d’URL du menu *Authentification > URI de redirection*. Si vous avez plusieurs URL de redirection, assurez-vous qu’il existe une nouvelle entrée correspondant à l’URI du service d’application pour chaque URL de redirection.

## <a name="community-help-and-support"></a>Aide et support de la communauté

Accédez à [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) pour obtenir un support de la part de la communauté.
Posez vos questions sur Stack Overflow d’abord et parcourez les problèmes déjà rencontrés pour voir si quelqu’un d’autre a déjà posé la même question.
Vérifiez que vos questions ou commentaires sont étiquetés [`adal` `msal` `dotnet`].

Si vous trouvez un bogue dans l’exemple, signalez-le sur [GitHub Issues](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Si vous trouvez un bogue dans MSAL.NET, signalez-le sur [MSAL.NET GitHub Issues](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Pour faire une suggestion, accédez à la [page User Voice](https://feedback.azure.com/forums/169401-azure-active-directory) suivante.

## <a name="next-steps"></a>Étapes suivantes
Découvrez-en plus sur les différents [flux d’authentification et scénarios d’applications](authentication-flows-app-scenarios.md) qui sont pris en charge par la plateforme d’identités Microsoft.

Pour plus d’informations, consultez la documentation conceptuelle suivante :

- [Locataires dans Azure Active Directory](single-and-multi-tenant-apps.md)
- [Comprendre les expériences de consentement de l’application Azure AD](application-consent-experience.md)
- [Guide pratique pour connecter un utilisateur Azure Active Directory à l’aide du modèle d’application multilocataire](howto-convert-app-to-be-multi-tenant.md)
- [Comprendre le consentement de l’utilisateur et de l’administrateur](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objets application et principal du service dans Azure Active Directory](app-objects-and-service-principals.md)
- [Démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft](quickstart-register-app.md)
- [Démarrage rapide : configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md).
- [Acquisition d’un jeton pour une application à l’aide des flux d’informations d’identification du client](msal-client-applications.md)

Pour obtenir un exemple plus simple d’application de démon de console multilocataire, consultez le [Guide de démarrage rapide du démon .NET Core](quickstart-v2-netcore-daemon.md).
