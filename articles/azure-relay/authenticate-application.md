---
title: S’authentifier à partir d’une application – Azure Relay (préversion)
description: Cet article fournit des informations sur l’authentification d’une application avec Azure Active Directory pour accéder aux ressources Azure Relay.
ms.topic: article
ms.date: 07/02/2021
ms.openlocfilehash: 2304dd58332cb95c40e7492451cf010ee46d76be
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654151"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-relay-entities-preview"></a>Authentifier et autoriser une application avec Azure Active Directory pour accéder aux entités Azure Relay (préversion)
Azure Relay prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes aux entités Azure Relay (connexions hybrides, relais WCF). Avec Azure AD, vous pouvez utiliser le contrôle d’accès en fonction du rôle d’Azure (Azure RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Pour en savoir plus sur les rôles et les attributions de rôles, consultez [Comprendre les différents rôles](../role-based-access-control/overview.md).   


[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="authenticate-from-an-app"></a>S’authentifier à partir d’une application
L’un des principaux avantages de l’utilisation d’Azure AD avec Azure Relay est que vous n’avez plus besoin de stocker vos informations d’identification dans votre code. À la place, vous pouvez demander un jeton d’accès OAuth 2.0 sur la plateforme d’identités Microsoft. Azure AD authentifie le principal de sécurité (un utilisateur, un groupe ou un principal de service) qui exécute l’application. Si l’authentification réussit, Azure AD renvoie le jeton d’accès à l’application, et l’application peut ensuite l’utiliser pour autoriser les requêtes adressées à Azure Relay.

Les sections suivantes vous montrent comment configurer votre application console pour l’authentification avec la plateforme d’identités Microsoft 2.0. Pour plus d’informations, consultez [Présentation de la plateforme d’identités Microsoft (v2.0)](../active-directory/develop/v2-overview.md).

Pour avoir une vue d’ensemble du flux d’octroi de code OAuth 2.0, consultez [Autoriser l’accès aux applications web Azure Active Directory à l’aide du flux d’octroi de code OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Inscrire votre application à un locataire Azure AD
La première étape de l’utilisation d’Azure AD pour autoriser l’accès aux entités Azure Relay est d’inscrire votre application cliente auprès d’un locataire Azure AD à partir du portail Azure. Lorsque vous inscrivez votre application cliente, vous fournissez des informations la concernant à Azure AD. Azure AD fournit ensuite un ID de client (appelé aussi ID d’application) que vous pouvez utiliser pour associer votre application au runtime Azure AD. 

Pour obtenir des instructions étape par étape sur l’inscription de votre application auprès d’Azure AD, consultez [Démarrage rapide : Inscrire une application auprès d’Azure AD](../active-directory/develop/quickstart-register-app.md#register-an-application).

> [!IMPORTANT]
> Notez l’**ID de répertoire (locataire)** et l’**ID d’application (client)** . Vous aurez besoin de ces valeurs pour exécuter l’exemple d’application.

### <a name="create-a-client-secret"></a>Créer une clé secrète client   
L’application a besoin d’une clé secrète client pour prouver son identité lors de la requête de jeton. Dans le même article dont le lien figure ci-dessus, consultez la section [Ajouter une clé secrète client](../active-directory/develop/quickstart-register-app.md#add-a-client-secret) pour créer une clé secrète client. 

> [!IMPORTANT]
> Notez la **clé secrète client**. Vous en aurez besoin pour exécuter l’exemple d’application.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Attribuer des rôles Azure à l’aide du portail Azure
Attribuez un des rôles Azure Relay au principal de service de l’application dans l’étendue souhaitée (entité Relay, espace de noms, groupe de ressources, abonnement). Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Téléchargez l’exemple d’application console à partir de [GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol).
1. Exécutez l’application localement sur votre ordinateur en suivant les instructions de l’[article README](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample).

    > [!NOTE]
    > Suivez les étapes décrites ci-dessus afin d’exécuter l’[exemple d’application console pour WCF Relay](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl). 

#### <a name="highlighted-code-from-the-sample"></a>Code en surbrillance de l’exemple
Voici le code de l’exemple qui montre comment utiliser l’authentification Azure AD pour se connecter au service Azure Relay.  

1. Créez un objet [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) à l’aide de la méthode `TokenProvider.CreateAzureActiveDirectoryTokenProvider`. 

    Si vous n’avez pas encore créé d’inscription d’application, consultez la section [Inscrire votre application auprès d’Azure AD](#register-your-application-with-an-azure-ad-tenant) pour la créer, puis créez une clé secrète client comme indiqué dans la section [Créer une clé secrète client](#create-a-client-secret).

    Si vous souhaitez utiliser une inscription d’application existante, suivez ces instructions pour obtenir l’**ID d’application (client)** et l’**ID de répertoire (locataire)** . 

    1. Connectez-vous au [portail Azure](https://portal.azure.com).
    1. Recherchez et sélectionnez **Azure Active Directory** à l’aide de la barre de recherche située en haut de la page.
    1. Sur la page **Azure Active Directory**, sélectionnez **Inscriptions d’applications** dans la section **Gérer** du menu de gauche. 
    1. Sélectionnez votre inscription d’application. 
    1. Sur la page de votre inscription d’application, vous verrez les valeurs de l’**ID d’application (client)** et de l’**ID de répertoire (locataire)** . 
    
    Pour obtenir la **clé secrète client**, procédez comme suit :
    1. Sur la page de votre inscription d’application, sélectionnez **Certificats et secrets** dans le menu de gauche. 
    1. Utilisez le bouton de copie dans la colonne **Valeur** pour le secret dans la section **Clés secrètes client**. 

    
    ```csharp
    static TokenProvider GetAadTokenProvider(string clientId, string tenantId, string clientSecret)
    {
        return TokenProvider.CreateAzureActiveDirectoryTokenProvider(
            async (audience, authority, state) =>
            {
                IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
                    .WithAuthority(authority)
                    .WithClientSecret(clientSecret)
                    .Build();

                var authResult = await app.AcquireTokenForClient(new [] { $"{audience}/.default" }).ExecuteAsync();
                return authResult.AccessToken;
            },
            $"https://login.microsoftonline.com/{tenantId}");
    }
    ```
1. Créez un objet [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) ou [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) en lui transmettant l’URI de la connexion hybride et le fournisseur de jetons que vous avez créé à l’étape précédente.

    **Écouteur :**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **Expéditeur :**
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur Azure RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md) ?
- Pour apprendre à attribuer et gérer les rôles Azure avec Azure PowerShell, Azure CLI ou l’API REST, consultez les articles suivants :
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide d’Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide de l’API REST](../role-based-access-control/role-assignments-rest.md)
    - [Ajouter ou supprimer des attributions de rôle Azure à l’aide de modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Pour en savoir plus sur Azure Relay, consultez les rubriques suivantes.
- [Qu’est-ce que Relay ?](relay-what-is-it.md)
- [Bien démarrer avec les WebSockets de connexions hybrides Azure Relay](relay-hybrid-connections-dotnet-get-started.md)
- [Bien démarrer avec les requêtes HTTP de connexions hybrides Azure Relay](relay-hybrid-connections-http-requests-dotnet-get-started.md)








