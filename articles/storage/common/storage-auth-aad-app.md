---
title: Authentification avec Azure Active Directory pour accéder aux objets blob et file d’attente des données à partir de votre application cliente
description: Utiliser Azure Active Directory pour authentifier à partir de dans une application cliente, acquérir un jeton OAuth 2.0 et autoriser les demandes de stockage Blob Azure et stockage file d’attente.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e45fe20e93d81c1cfd1f868b40f76743558758bb
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754955"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Authentification avec Azure Active Directory à partir d’une application pour l’accès aux objets BLOB et files d’attente

Des principaux avantages de l’utilisation d’Azure Active Directory (Azure AD) avec le stockage Blob Azure ou stockage file d’attente sont que vos informations d’identification sans doivent être stockées dans votre code. Au lieu de cela, vous pouvez demander un jeton d’accès OAuth 2.0 à partir de la plateforme d’identité Microsoft (anciennement Azure AD). Azure AD authentifie l’entité de sécurité (utilisateur, groupe ou principal de service) qui exécute l’application. Si l’authentification réussit, Azure AD retourne le jeton d’accès à l’application et l’application peut ensuite utiliser le jeton d’accès pour autoriser les demandes pour le stockage Blob Azure ou stockage file d’attente.

Cet article explique comment configurer votre application native ou une application web pour l’authentification auprès d’Azure AD. L’exemple de code utilise .NET, mais l’approche est similaire avec d’autres langages.

Pour avoir une vue d’ensemble du flux d’octroi de code OAuth 2.0, consultez [Autoriser l’accès aux applications web Azure Active Directory à l’aide du flux d’octroi de code OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Attribuer un rôle RBAC à un principal de sécurité Azure AD

Pour authentifier un principal de sécurité à partir de votre application Stockage Azure, commencez par configurer les paramètres de contrôle d’accès en fonction du rôle (RBAC) pour ce principal de sécurité. Stockage Azure définit les rôles RBAC intégrés qui englobent les autorisations pour les conteneurs et les files d’attente. Quand le rôle RBAC est attribué à un principal de sécurité, ce dernier obtient l’accès à cette ressource. Pour plus d’informations, consultez [gérer les droits d’accès aux données d’objets Blob Azure et de file d’attente avec RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Inscrire votre application à un locataire Azure AD

La première étape à l’aide d’Azure AD pour autoriser l’accès aux ressources de stockage inscrit votre application cliente avec un locataire Azure AD à partir de la [Azure portal](https://portal.azure.com). Lorsque vous inscrivez votre application cliente, vous fournissez des informations sur l’application à Azure AD. Azure AD fournit ensuite un ID de client (appelé aussi *ID d’application*) que vous utilisez pour associer votre application à Azure AD au moment de l’exécution. Pour en savoir plus sur l’ID de client, consultez [Objets application et principal du service dans Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Pour inscrire votre application Azure Storage, suivez les étapes indiquées dans [Guide de démarrage rapide : Inscrire une application avec la plateforme Microsoft identity](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). L’illustration suivante montre les paramètres communs d’inscription d’une application web :

![Capture d’écran montrant comment inscrire votre application de stockage avec Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Si vous inscrivez votre application comme une application native, vous pouvez spécifier n’importe quel URI valide pour **l’URI de redirection**. Pour les applications natives, cette valeur ne devra pas être une URL réelle. Pour les applications web, l’URI de redirection doit être un URI valide, car elle spécifie l’URL à laquelle les jetons sont fournis.

Une fois votre application inscrite, l’ID d’application (ou ID de client) se trouve sous **Paramètres** :

![Capture d’écran montrant l’ID client](./media/storage-auth-aad-app/app-registration-client-id.png)

Pour plus d’informations sur l’inscription d’une application dans Azure AD, consultez [Intégration d’applications à Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Accorder à votre application inscrite des autorisations sur le Stockage Azure

Ensuite, accorder vos autorisations d’application pour appeler des API de stockage Azure. Cette étape permet à votre application autoriser les demandes vers le stockage Azure avec Azure AD.

1. Sur le **vue d’ensemble** page de votre application enregistrée, sélectionnez **affichage des autorisations d’API**.
1. Dans le **autorisations d’API** section, sélectionnez **ajouter une autorisation** et choisissez **mon organisation utilise des API**.
1. Sous le **mon organisation utilise des API** section, recherchez « Azure Storage » et sélectionnez **stockage Azure** à partir de la liste des résultats pour afficher le **autorisations d’API de requête** volet.

    ![Capture d’écran montrant les autorisations pour le stockage](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. Sous **quel type d’autorisations votre application nécessite-t-elle ?** , notez que le type d’autorisation disponibles est **autorisations déléguées**. Cette option est sélectionnée pour vous par défaut.
1. Dans le **sélectionner les autorisations** section de la **autorisations d’API demande** volet, activez la case à cocher en regard **user_impersonation**, puis cliquez sur **ajouter autorisations**.
1. Le **autorisations d’API** volet affiche à présent que votre application Azure AD a accès à Microsoft Graph et le stockage Azure. Autorisations Microsoft Graph sont accordées automatiquement lorsque vous commencez par inscrire votre application auprès d’Azure AD.

    ![Capture d’écran montrant inscrire les autorisations d’application](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="libraries-for-token-acquisition"></a>Bibliothèques pour l’acquisition du jeton

Une fois que vous avez inscrit votre application et les autorisations pour accéder aux données dans le stockage Blob Azure ou stockage file d’attente, vous pouvez ajouter le code à votre application pour authentifier un principal de sécurité et d’acquérir un jeton OAuth 2.0. Pour authentifier et acquérir le jeton, vous pouvez utiliser l’une de le [bibliothèques d’authentification Microsoft identity plateforme](../../active-directory/develop/reference-v2-libraries.md) ou une autre bibliothèque open source qui prend en charge OpenID Connect 1.0. Votre application peut ensuite utiliser le jeton d’accès pour autoriser une demande de stockage Blob Azure ou stockage file d’attente.

Pour obtenir la liste de scénarios pour lesquels l’acquisition de jetons est pris en charge, consultez le [scénarios](https://aka.ms/msal-net-scenarios) section de la [Microsoft Authentication Library (MSAL) pour .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) référentiel GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Exemple de code .NET : Créer un objet blob de blocs

L’exemple de code montre comment obtenir un jeton d’accès d’Azure AD. Le jeton d’accès est utilisé pour authentifier l’utilisateur spécifié et autoriser une demande de création d’objet blob de blocs. Pour que cet exemple fonctionne, suivez d’abord les étapes décrites dans les sections précédentes.

> [!NOTE]
> En tant que propriétaire de votre compte de stockage Azure, vous n’avez pas automatiquement l’autorisation d’accéder aux données. Vous devez explicitement vous attribuer un rôle RBAC pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, groupe de ressources, compte de stockage, conteneur ou file d’attente.
>
> Par exemple, pour exécuter l’exemple de code sur un compte de stockage dont vous êtes propriétaire et sous votre propre identité d’utilisateur, vous devez vous attribuer le rôle RBAC Contributeur aux données blob. Sinon, l’appel pour créer l’objet blob échoue avec le code d’état HTTP 403 (Interdit). Pour plus d’informations, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valeurs connues pour l’authentification avec Azure AD

Pour authentifier un principal de sécurité avec Azure AD, vous devez inclure des valeurs connues dans votre code.

#### <a name="azure-ad-authority"></a>Autorité Azure AD

Pour le cloud public Microsoft, l’autorité Azure AD de base est comme suit, où *tenant-id* est votre ID de locataire Active Directory (ou ID de répertoire) :

`https://login.microsoftonline.com/<tenant-id>/`

L’ID client identifie le client Azure AD pour l’authentification. Pour récupérer l’ID de locataire, suivez les étapes décrites dans la section intitulée **obtenir l’ID client pour votre Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID de ressource de stockage

Utilisez l’ID de ressource de stockage Azure pour acquérir un jeton d’autorisation des requêtes dans stockage Azure :

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obtenir l’ID client pour votre Azure Active Directory

Pour obtenir l’ID client, procédez comme suit :

1. Dans le portail Azure, sélectionnez votre Active Directory.
2. Cliquez sur **Propriétés**.
3. Copiez la valeur GUID fournie pour l’**ID de répertoire**. Cette valeur est également appelée l’ID client.

![Capture d’écran montrant comment copier l’ID de locataire](./media/storage-auth-aad-app/aad-tenant-id.png)

## <a name="set-up-a-basic-web-app-to-authenticate-to-azure-ad"></a>Configurer une application web de base pour s’authentifier auprès d’Azure AD

Lorsque votre application accède au stockage Azure, il ainsi de suite l’utilisateur. Pour tester cet exemple de code, vous avez besoin d’une application qui invite l’utilisateur peut se connecter à l’aide d’une identité Azure AD. Vous pouvez télécharger cet [exemple de code](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) pour tester une application web de base qui s’authentifie avec votre compte Azure AD.

### <a name="completed-sample"></a>Exemple terminé

Une version opérationnelle complète de l’exemple de code présenté dans cet article peut être téléchargée à partir de [GitHub](http://aka.ms/aadstorage). Examen et exécution de l’exemple complet peuvent être utiles pour comprendre les exemples de code.

### <a name="add-references-and-using-statements"></a>Ajouter des références et des instructions using  

À partir de Visual Studio, installez la bibliothèque cliente Azure Storage. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du gestionnaire de package**. Tapez les commandes suivantes dans la fenêtre de console pour installer les packages nécessaires à partir de la bibliothèque cliente Azure Storage pour .NET :

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Ensuite, ajoutez le code suivant à l’aide des instructions pour le fichier HomeController.cs :

```csharp
using System;
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="create-a-block-blob"></a>Créer un objet blob de blocs

Ajoutez l’extrait de code suivant pour créer un objet blob de blocs :

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/sample-container/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Pour autoriser les opérations d’objet blob et file d’attente avec un jeton OAuth 2.0, vous devez utiliser HTTPS.

Dans l’exemple ci-dessus, la bibliothèque cliente .NET gère l’autorisation de la demande de création de l’objet blob de blocs. Les bibliothèques de client stockage Azure pour d’autres langages également gérer l’autorisation de la demande pour vous. Toutefois, si vous appelez une opération Stockage Azure avec un jeton OAuth à l’aide de l’API REST, puis vous devez autoriser la demande en utilisant le jeton OAuth.

Pour appeler les opérations des services Blob et de File d’attente à l’aide des jetons d’accès OAuth, passez le jeton d’accès dans l’en-tête **Authorization** en utilisant le schéma **Bearer** et spécifiez une version de service 2017-11-09 ou ultérieure, comme indiqué dans l’exemple suivant :

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Obtenir un jeton OAuth d’Azure AD

Ensuite, ajoutez une méthode qui demande un jeton à Azure AD. Le jeton que vous demandez sera pour le compte de l’utilisateur, et nous allons utiliser la méthode GetTokenOnBehalfOfUser.

Pour demander le jeton, vous aurez besoin des valeurs suivantes à partir de l’inscription de votre application,

- ID de locataire (ou répertoire)
- ID de client (ou Application)
- URI de redirection du client

N’oubliez pas que si vous avez simplement connecté, et que vous demandez un jeton pour le `storage.azure.com` ressource, vous devez présenter à l’utilisateur avec une interface utilisateur où l’utilisateur peut donner son consentement à une telle action en leur nom. Pour faciliter la que vous avez besoin intercepter les `MsalUiRequiredException` et ajoutez la fonctionnalité permettant de demander le consentement de l’utilisateur, comme indiqué dans l’exemple suivant :

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Le consentement est le processus via lequel un utilisateur autorise une application à accéder à des ressources protégées en son nom. La plateforme Microsoft identity 2.0 prend en charge le consentement incrémentiel, ce qui signifie qu’une entité de sécurité peut demander un jeu d’autorisations minimum initialement et ajouter des autorisations au fil du temps en fonction des besoins. Lorsque votre code demande un jeton d’accès, spécifiez l’étendue des autorisations dont votre application a besoin à un moment donné par dans le `scope` paramètre. La méthode suivante construit les propriétés d’authentification pour solliciter le consentement incrémentiel :

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la plateforme Microsoft identity, consultez [plateforme d’identité Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Pour en savoir plus sur les rôles RBAC pour le stockage Azure, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour en savoir plus sur l’utilisation des identités gérées pour les ressources Azure avec le stockage Azure, consultez [authentifier l’accès aux objets BLOB et files d’attente avec Azure Active Directory et des identités gérées pour les ressources Azure](storage-auth-aad-msi.md).
