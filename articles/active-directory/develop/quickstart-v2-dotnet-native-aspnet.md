---
title: Appeler une API web ASP.NET protégée par la plateforme d’identités Microsoft
description: Ce guide de démarrage rapide explique comment appeler une API web ASP.NET protégée par la plateforme d’identités Microsoft à partir d’une application de bureau Windows (WPF). Le client WPF authentifie un utilisateur, demande un jeton d’accès et appelle l’API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 0fc31fd397f8206f7c6f0509dd03495631dde609
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165632"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Démarrage rapide : Appeler une API web ASP.NET protégée par la plateforme d’identités Microsoft

Dans ce guide de démarrage rapide, vous exposez une API web et la protégez afin que seul un utilisateur authentifié puisse y accéder. Cet exemple montre comment exposer une API web ASP.NET pour qu’elle accepte des jetons émis par des comptes personnels (dont outlook.com, live.com, etc.) ainsi que des comptes professionnels ou scolaires de toute société ou organisation intégrée à la plateforme d’identités Microsoft.

L’exemple inclut également un client d’application de bureau Windows (WPF) qui montre comment demander un jeton d’accès pour accéder à une API web.

## <a name="prerequisites"></a>Prérequis

Pour exécuter cet exemple, vous avez besoin des éléments suivants :

* Visual Studio 2017 ou 2019.  Téléchargez [Visual Studio gratuitement](https://www.visualstudio.com/downloads/).

* Un [compte Microsoft](https://www.outlook.com) ou un [programme pour développeurs Microsoft 365](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Télécharger ou cloner cet exemple

Vous pouvez cloner cet exemple à partir de votre interpréteur de commandes ou de votre ligne de commande :

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Vous pouvez également [télécharger l’exemple en tant que fichier .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Inscrire votre API web dans le portail d’inscription d’applications

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Choisissez le locataire Azure AD où vous voulez créer vos applications.

Si vous souhaitez inscrire vos applications manuellement, commencez par effectuer les opérations suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte est présent dans plusieurs locataires Azure AD, sélectionnez votre profil en haut à droite dans le menu en haut de la page, puis **changer d’annuaire**.
   Modifiez votre session de portail en la définissant sur le client Azure AD souhaité.

### <a name="register-the-service-app-todolistservice"></a>Inscrire l’application de service (TodoListService)

1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Modifiez les **Types de comptes pris en charge** en sélectionnant **Comptes dans un annuaire organisationnel**.
   - Sélectionnez **Inscrire** pour créer l’application.

1. Dans la page **Vue d’ensemble** de l’application, recherchez la valeur **ID d’application (client)** et notez-la. Vous en aurez besoin pour configurer le fichier de configuration Visual Studio pour ce projet (`ClientId` dans `TodoListService\Web.config`).
1. Sélectionnez la section **Exposer une API**, puis :
   - Sélectionnez **Ajouter une étendue**.
   - Acceptez l’URI d’ID d’application proposé (api://{clientId}) en sélectionnant **Enregistrer et continuer**.
   - Entrez les paramètres suivants :
     - Pour **Nom de l’étendue**, utilisez `access_as_user`.
     - Vérifiez que l’option **Administrateurs et utilisateurs** est sélectionnée pour **Qui peut donner son consentement**.
     - Dans **Nom d’affichage du consentement de l’administrateur**, tapez `Access TodoListService as a user`.
     - Dans **Description du consentement de l’administrateur**, tapez `Accesses the TodoListService web API as a user`.
     - Dans **Nom d’affichage du consentement de l’utilisateur**, tapez `Access TodoListService as a user`.
     - Dans **Description du consentement de l’utilisateur**, tapez `Accesses the TodoListService web API as a user`.
     - Conservez l’**État** **activée**.
     - Sélectionnez **Ajouter une étendue**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Configurer le projet de service en fonction de l’API web inscrite

1. Ouvrez la solution dans Visual Studio, puis ouvrez le fichier **Web. config** sous la racine du projet **TodoListService**.
1. Remplacez la valeur du paramètre `ida:ClientId` par l’**ID client (ID d’application)** de l’application que vous venez d’inscrire dans le portail d’inscription d’application.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Ajouter une nouvelle étendue à l’app.config de *TodoListClient*

* Ouvrez le fichier **app.config** situé dans dossier racine du projet **TodoListClient**, puis collez l’**ID d’application** à partir de l’application que vous venez d’inscrire pour votre *TodoListService* sous le paramètre `TodoListServiceScope`, en remplaçant la chaîne `{Enter the Application ID of your TodoListService from the app registration portal}`.

  > [!NOTE]
  > Assurez-vous que le format utilisé est le suivant :
  >
  > `api://{TodoListService-Application-ID}/access_as_user`
  >
  >(où {TodoListService-application-ID} est le GUID représentant l’ID d’application de votre TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Inscrire l’application cliente (TodoListClient)

Dans cette étape, vous configurez votre projet *TodoListClient* en inscrivant une nouvelle application dans le portail d’inscription d’application. Dans les cas où le client et le serveur sont considérés comme *la même application*, vous pouvez aussi simplement réutiliser la même application inscrite à l’étape 2. L’utilisation de la même application est nécessaire si vous voulez que les utilisateurs se connectent avec des comptes personnels Microsoft.

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Inscrire l’application *TodoListClient* dans le *portail d’inscription d’applications*

1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `NativeClient-DotNet-TodoListClient`.
   - Modifiez les **Types de comptes pris en charge** en sélectionnant **Comptes dans un annuaire organisationnel**.
   - Sélectionnez **Inscrire** pour créer l’application.
   
   > [!NOTE]
   > Dans le projet **app.config** de *TodoListClient*, la valeur par défaut de `ida:Tenant` est définie sur `common`.
   >
   > `common` signifie que vous pouvez vous connecter à l’aide d’un compte professionnel ou scolaire ou un compte Microsoft personnel (car vous avez sélectionné **Comptes dans un annuaire organisationnel**).
   >
   > `organizations` signifie que vous pouvez vous connecter à l’aide d’un compte professionnel ou scolaire.
   >
   > `consumers` signifie que vous pouvez vous connecter à l’aide d’un compte Microsoft personnel uniquement.
   >
   
1. Sélectionnez la section **Authentification** dans la page de vue d’ensemble de l’application.
   1. Sous **Configurations de plateformes**, sélectionnez le bouton **Ajouter une plateforme**.
   1. Pour les **Applications de bureau et mobiles**, sélectionnez **Application de bureau et mobiles**.
   1. Pour les **URI de redirection**, activez la case à cocher **https://login.microsoftonline.com/common/oauth2/nativeclient** .
   1. Sélectionnez **Configurer**.   
1. Sélectionnez la section **Autorisations de l’API** :
   1. Cliquez sur le bouton **Ajouter une autorisation**.
   1. Sélectionnez l’onglet **Mes API**.
   1. Dans la liste des API, sélectionnez `AppModelv2-NativeClient-DotNet-TodoListService API` ou le nom que vous avez entré pour l’API web.
   1. Si ce n’est déjà fait, vérifiez l’autorisation **access_as_user**. Utilisez la zone de recherche au besoin.
   1. Sélectionnez le bouton **Ajouter des autorisations**.

### <a name="configure-your-todolistclient-project"></a>Configurer votre projet *TodoListClient*

1. Dans le *portail d’inscription d’application*, dans la page **Vue d’ensemble**, copiez la valeur de l’**ID d’application (client)**
1. Ouvrez le fichier **app.config** situé dans le dossier racine du projet **TodoListClient**, puis collez la valeur dans la valeur du paramètre `ida:ClientId`

## <a name="run-your-project"></a>Exécuter votre projet

1. Appuyez sur `<F5>` pour exécuter votre projet. Votre *TodoListClient* doit s’ouvrir.
1. Sélectionnez **Se connecter** en haut à droite, puis connectez-vous avec l’ID d’utilisateur que vous avez utilisé pour inscrire votre application ou celui d’un utilisateur figurant dans le même annuaire.
1. À ce stade, si vous vous connectez pour la première fois, vous pouvez être invité à donner son consentement à l’API web *TodoListService*.
1. La connexion demande également le jeton d’accès à l’étendue *access_as_user* afin d’accéder à l’API web *TodoListService* et manipuler la liste *To-Do*.

## <a name="pre-authorize-your-client-application"></a>Pré-autoriser votre application cliente

L’une des façons d’autoriser des utilisateurs d’autres annuaires à accéder à votre API web consiste à *pré-autoriser* les applications clientes à accéder à votre API web en ajoutant les ID d’application des applications clientes dans la liste des applications *pré-autorisées* pour votre API web. En ajoutant un client pré-autorisé, vous ne devez pas demander à l’utilisateur de consentir à utiliser votre API web. Pour pré-autoriser votre application Web, procédez comme suit :

1. Revenez au *portail d’inscription d’application*, puis ouvrez les propriétés de votre **TodoListService**.
1. Dans la section **Exposer une API**, cliquez sur **Ajouter une application cliente** dans la section *Applications clientes autorisées*.
1. Dans le champ *ID client*, collez l’ID de l’application `TodoListClient`.
1. Dans la section *Étendues autorisées*, sélectionnez l’étendue de cette API web `api://<Application ID>/access_as_user`.
1. Appuyez sur le bouton **Ajouter une application** au bas de la page.

## <a name="run-your-project"></a>Exécuter votre projet

1. Appuyez sur `<F5>` pour exécuter votre projet. Votre *TodoListClient* doit s’ouvrir.
1. Sélectionnez **Se connecter** en haut à droite (ou Effacer le cache/Se connecter), puis connectez-vous à l’aide d’un compte Microsoft personnel (live.com ou hotmail.com) ou d’un compte professionnel ou scolaire.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Facultatif : Restreindre les utilisateurs qui peuvent se connecter à votre application

Par défaut, quand vous téléchargez cet exemple de code et configurez l’application pour utiliser le point de terminaison Azure Active Directory v2 en suivant les étapes précédentes, tant les comptes personnels (outlook.com, live.com, etc.) que les comptes professionnels ou scolaires de toutes les organisations intégrées à Azure AD peuvent demander des jetons et accéder à votre API web.

Pour limiter les utilisateurs autorisés à se connecter à votre application, utilisez l’une des options suivantes :

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Option 1 : Restreindre l’accès à une seule organisation (locataire unique)

Vous pouvez restreindre l’accès à votre application aux comptes d’utilisateur qui se trouvent dans un seul locataire Azure AD, y compris les *comptes invités* de ce locataire. Ce scénario est courant pour les *applications métier* :

1. Ouvrez le fichier **App_Start\Startup.Auth** et modifiez la valeur du point de terminaison de métadonnées passée dans le `OpenIdConnectSecurityTokenProvider` à `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (vous pouvez également utiliser le nom du locataire, par exemple `contoso.onmicrosoft.com`).
2. Dans le même fichier, définissez la propriété `ValidIssuer` de `TokenValidationParameters` sur `"https://sts.windows.net/{Tenant ID}/"` et l’argument `ValidateIssuer` sur `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Option n°2 : Utiliser une méthode personnalisée pour valider les émetteurs

Vous pouvez implémenter une méthode personnalisée pour valider les émetteurs à l’aide du paramètre **IssuerValidator**. Pour plus d’informations sur l’utilisation de ce paramètre, lisez cette rubrique concernant la [classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur le scénario d’API web protégée pris en charge par la Plateforme d’identité Microsoft :
> [!div class="nextstepaction"]
> [Scénario d’API web protégée](scenario-protected-web-api-overview.md)
