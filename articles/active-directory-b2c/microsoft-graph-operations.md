---
title: Gérer les ressources avec Microsoft Graph
titleSuffix: Azure AD B2C
description: Découvrez comment gérer les ressources dans un locataire Azure AD B2C en appelant l’API Microsoft Graph et en utilisant une identité d’application pour automatiser le processus.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 834afe8d0f0c5c53c2cb300d666dbcccbee46ec0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962426"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gérer Azure AD B2C avec Microsoft Graph

Microsoft Graph vous permet de gérer les ressources dans votre répertoire Azure AD B2C. Les opérations suivantes de l’API Microsoft Graph sont prises en charge pour la gestion de ressources Azure AD B2C, dont les utilisateurs, les fournisseurs d’identité, les flux d’utilisateurs, les stratégies personnalisées et les clés de stratégies. Chaque lien dans les sections suivantes cible la page correspondante dans la référence de l’API Microsoft Graph pour cette opération. 

> [!NOTE]
> Vous pouvez également créer par programmation un répertoire Azure AD B2C, ainsi que la ressource Azure correspondante liée à un abonnement Azure. Cette fonctionnalité n’est pas exposée via l’API Microsoft Graph, mais via l’API REST Azure. Pour plus d’informations, consultez [Locataires B2C – Créer](/rest/api/activedirectory/b2ctenants/create).

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API MS Graph et interagir avec les ressources de votre locataire Azure AD B2C, vous avez besoin d’une inscription d’application qui accorde les autorisations nécessaires. Suivez les étapes décrites dans l’article [Gérer Azure AD B2C avec Microsoft Graph](microsoft-graph-get-started.md) pour créer une inscription d’application que votre application de gestion peut utiliser. 

## <a name="user-management"></a>User Management

- [Répertorier les utilisateurs](/graph/api/user-list)
- [Créer un utilisateur consommateur](/graph/api/user-post-users)
- [Obtenir un utilisateur](/graph/api/user-get)
- [Mettre à jour un utilisateur](/graph/api/user-update)
- [Suppression d’un utilisateur](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Gestion des numéros de téléphone des utilisateurs (bêta)

Numéro de téléphone pouvant être utilisé par un utilisateur pour se connecter à l’aide de [SMS ou appels vocaux](identity-provider-local.md#phone-sign-in)ou de l’[authentification multifacteur](multi-factor-authentication.md). Pour plus d’informations, consultez [API des méthodes d’authentification Azure AD](/graph/api/resources/phoneauthenticationmethod).

- [Ajouter](/graph/api/authentication-post-phonemethods)
- [Liste](/graph/api/authentication-list-phonemethods)
- [Get](/graph/api/phoneauthenticationmethod-get)
- [Mettre à jour](/graph/api/phoneauthenticationmethod-update)
- [Supprimer](/graph/api/phoneauthenticationmethod-delete)

Notez que l’opération [list](/graph/api/authentication-list-phonemethods) retourne uniquement les numéros de téléphone activés. Le numéro de téléphone suivant doit être activé pour pouvoir être utilisé avec les opérations « list ». 

![Activer la connexion par téléphone](./media/microsoft-graph-operations/enable-phone-sign-in.png)

> [!NOTE]
> Dans la version bêta actuelle, cette API fonctionne uniquement si le numéro de téléphone est stocké avec un espace entre l’indicatif du pays et le numéro de téléphone. Par défaut, le service Azure AD B2C n’ajoute pas cet espace.

## <a name="self-service-password-reset-email-address-beta"></a>Adresse e-mail de réinitialisation de mot de passe en libre-service (bêta)

Adresse e-mail pouvant être utilisée par un [compte de connexion nom_utilisateur](identity-provider-local.md#username-sign-in) pour réinitialiser le mot de passe. Pour plus d’informations, consultez [API des méthodes d’authentification Azure AD](/graph/api/resources/emailauthenticationmethod).

- [Ajouter](/graph/api/emailauthenticationmethod-post)
- [Liste](/graph/api/emailauthenticationmethod-list)
- [Get](/graph/api/emailauthenticationmethod-get)
- [Mettre à jour](/graph/api/emailauthenticationmethod-update)
- [Supprimer](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Fournisseurs d’identité

Gérez les [fournisseurs d’identité](add-identity-provider.md) disponibles pour vos flux d’utilisateurs dans votre locataire Azure AD B2C.

- [Répertorier les fournisseurs d’identité inscrits dans le locataire Azure AD B2C](/graph/api/identityprovider-list)
- [Créer un fournisseur d’identité](/graph/api/identityprovider-post-identityproviders)
- [Obtenir un fournisseur d’identité](/graph/api/identityprovider-get)
- [Mettre à jour un fournisseur d’identité](/graph/api/identityprovider-update)
- [Supprimer un fournisseur d’identité](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Flux utilisateur

Configurez des stratégies prédéfinies pour l’inscription, la connexion, l’inscription et la connexion combinées, la réinitialisation de mot de passe et la mise à jour de profil.

- [Répertorier des flux d’utilisateur](/graph/api/identitycontainer-list-b2cuserflows)
- [Créer un flux utilisateur](/graph/api/identitycontainer-post-b2cuserflows)
- [Obtenir un flux d’utilisateur](/graph/api/b2cidentityuserflow-get)
- [Supprimer un flux d’utilisateur](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Méthodes d’authentification de flux d’utilisateur (bêta)

Choisissez un mécanisme pour permettre aux utilisateurs de s’inscrire via des comptes locaux. Les comptes locaux sont les comptes pour lesquels Azure AD effectue l’assertion d’identité. Pour plus d’informations, consultez [Type de ressource b2cAuthenticationMethodsPolicy](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [Mettre à jour](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Stratégies personnalisées

Les opérations suivantes vous permettent de gérer vos stratégies d’infrastructure de confiance Azure AD B2C, appelées [stratégies personnalisées](custom-policy-overview.md).

- [Répertorier toutes les stratégies d’infrastructure de confiance configurées dans un locataire](/graph/api/trustframework-list-trustframeworkpolicies)
- [Créer une stratégie d’infrastructure de confiance](/graph/api/trustframework-post-trustframeworkpolicy)
- [Lire les propriétés d’une stratégie d’infrastructure de confiance existante](/graph/api/trustframeworkpolicy-get)
- [Mettre à jour ou créer une stratégie d’infrastructure de confiance](/graph/api/trustframework-put-trustframeworkpolicy)
- [Supprimer une stratégie d’infrastructure de confiance existante](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Clés de stratégies

L’infrastructure Identity Experience Framework stocke les secrets référencés dans une stratégie personnalisée pour établir la confiance entre des composants. Ces secrets peuvent être des clés/valeurs symétriques ou asymétriques. Dans le Portail Azure, ces entités sont affichées en tant que **clés de stratégies**.

La ressource de niveau supérieur pour les clés de stratégies dans l’API Microsoft Graph est le [jeu de clés de l’infrastructure de confiance](/graph/api/resources/trustframeworkkeyset). Chaque **jeu de clés** contient au moins une **clé**. Pour créer une clé, commencez par créer un jeu de clés vide, puis générez une clé dans celui-ci. Vous pouvez créer un secret manuel, charger un certificat ou une clé PKCS12. La clé peut être un secret généré, une chaîne (par exemple, un secret de l’application Facebook) ou un certificat que vous chargez. Si un jeu de clés compte plusieurs clés, une seule d’entre elles est active.

### <a name="trust-framework-policy-keyset"></a>Jeu de clés de stratégie d’infrastructure de confiance

- [Répertorier les jeux de clés d’infrastructure de confiance](/graph/api/trustframework-list-keysets)
- [Créer des jeux de clés d’une infrastructure de confiance](/graph/api/trustframework-post-keysets)
- [Obtenir un jeu de clés](/graph/api/trustframeworkkeyset-get)
- [Mettre à jour des jeux de clés d’une infrastructure de confiance](/graph/api/trustframeworkkeyset-update)
- [Supprimer des jeux de clés d’une infrastructure de confiance](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Clé de stratégie d’infrastructure de confiance

- [Obtenir une clé actuellement active dans le jeu de clés](/graph/api/trustframeworkkeyset-getactivekey)
- [Générer une clé dans un jeu de clés](/graph/api/trustframeworkkeyset-generatekey)
- [Charger un secret basé sur une chaîne](/graph/api/trustframeworkkeyset-uploadsecret)
- [Charger un certificat X.509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Charger un certificat au format PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Applications

- [Liste des applications](/graph/api/application-list)
- [Créer une application](/graph/api/resources/application)
- [Mettre à jour une application](/graph/api/application-update)
- [Créer un principal de service](/graph/api/resources/serviceprincipal)
- [Créer une allocation oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Supprimer une application](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Propriétés d’extension d’application

- [Répertorier des propriétés d’extension](/graph/api/application-list-extensionproperty)

Azure AD B2C fournit un annuaire pouvant contenir 100 attributs personnalisés par utilisateur. Pour des flux d’utilisateurs, ces propriétés d’extension sont [gérées à l’aide du portail Azure](user-flow-custom-attributes.md). Pour des stratégies personnalisées, Azure AD B2C crée la propriété pour vous la première fois que la stratégie écrit une valeur dans la propriété d’extension.

## <a name="audit-logs"></a>Journaux d’audit

- [Répertorier des journaux d’audit](/graph/api/directoryaudit-list)

Pour plus d’informations sur l’accès aux journaux d’audit Azure AD B2C, consultez [Accès aux journaux d’audit Azure AD B2C](view-audit-logs.md).

## <a name="conditional-access"></a>Accès conditionnel

- [Répertorier toutes les stratégies d’accès conditionnel](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Lire les propriétés et les relations d’une stratégie d’accès conditionnel](/graph/api/conditionalaccesspolicy-get)
- [Créer une stratégie d’accès conditionnel](/graph/api/resources/application)
- [Mettre à jour une stratégie d’accès conditionnel](/graph/api/conditionalaccesspolicy-update)
- [Supprimer une stratégie d’accès conditionnel](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Exemple de code : Comment gérer les comptes d’utilisateurs par programmation

Cet exemple de code est une application console .NET Core qui utilise le [SDK Microsoft Graph](/graph/sdks/sdks-overview) pour interagir avec l’API Microsoft Graph. Son code illustre comment appeler l’API pour gérer par programmation les utilisateurs dans un locataire Azure AD B2C.
Vous pouvez [télécharger l’archive de l’exemple](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), [parcourir le dépôt](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) sur GitHub ou cloner le dépôt :

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Une fois que vous avez obtenu l’exemple de code, configurez-le pour votre environnement, puis générez le projet :

1. Ouvrez le projet dans [Visual Studio](https://visualstudio.microsoft.com) ou dans [Visual Studio Code](https://code.visualstudio.com).
1. Ouvrez `src/appsettings.json`.
1. Dans la section `appSettings`, remplacez `your-b2c-tenant` par le nom de votre locataire et `Application (client) ID` et `Client secret` par les valeurs de l’inscription de votre application de gestion. Pour plus d’informations, consultez [Inscrire une application Microsoft Graph](microsoft-graph-get-started.md).
1. Ouvrez une fenêtre de console dans votre clone local du dépôt, accédez au répertoire `src`, puis générez le projet :

    ```console
    cd src
    dotnet build
    ```
    
1. Exécutez l’application avec la commande `dotnet` :

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

L’application affiche une liste de commandes que vous pouvez exécuter. Par exemple, obtenir tous les utilisateurs, obtenir un seul utilisateur, supprimer un utilisateur, mettre à jour le mot de passe d’un utilisateur et importer en bloc.

### <a name="code-discussion"></a>Considérations sur le code

L’exemple de code utilise le kit [SDK Microsoft Graph](/graph/sdks/sdks-overview), qui est conçu pour simplifier la création d’applications qualitatives, efficaces et résilientes qui accèdent à Microsoft Graph.

Toute requête envoyée à l’API Microsoft Graph nécessite un jeton d’accès pour l’authentification. La solution utilise le package NuGet [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) qui fournit un wrapper basé sur un scénario d’authentification de la bibliothèque d’authentification Microsoft (MSAL) à utiliser avec le SDK Microsoft Graph.

La méthode `RunAsync` dans le fichier _Program.cs_ :

1. Lit les paramètres d’application à partir du fichier _appsettings.json_
1. Initialise le fournisseur d’authentification en utilisant le flux d’[octroi d’informations d’identification du client OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Avec le flux d’octroi d’informations d’identification du client, l’application est en mesure d’obtenir un jeton d’accès pour appeler l’API Microsoft Graph.
1. Configure le client du service Microsoft Graph avec le fournisseur d’authentification :

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Le *GraphServiceClient* initialisée est ensuite utilisé dans _UserService.cs_ pour effectuer les opérations de gestion des utilisateurs. Par exemple, pour obtenir une liste des comptes d’utilisateur dans le locataire :

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Faire des appels d’API en utilisant le SDK Microsoft Graph](/graph/sdks/create-requests) contient des informations sur la façon de lire et d’écrire des informations à partir de Microsoft Graph, d’utiliser `$select` pour contrôler les propriétés retournées, de fournir des paramètres de requête personnalisés, et d’utiliser les paramètres de requête `$filter` et `$orderBy`.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)