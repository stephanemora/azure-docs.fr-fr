---
title: Gérer les utilisateurs avec l’API Microsoft Graph
titleSuffix: Azure AD B2C
description: Découvrez comment gérer les utilisateurs dans un locataire Azure AD B2C en appelant l’API Microsoft Graph et en utilisant une identité d’application pour automatiser le processus.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178872"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gérer les comptes d’utilisateur Azure AD B2C avec Microsoft Graph

Microsoft Graph vous permet de gérer les comptes d’utilisateur dans votre annuaire Azure AD B2C en fournissant des méthodes de création, de lecture, de mise à jour et de suppression dans l’API Microsoft Graph. Vous pouvez migrer un magasin d’utilisateurs existant vers un locataire Azure AD B2C et effectuer d’autres opérations de gestion des comptes d’utilisateur en appelant l’API Microsoft Graph.

Dans les sections qui suivent, les principaux aspects de la gestion des utilisateurs Azure AD B2C avec l’API Microsoft Graph sont présentés. Les opérations, les types et les propriétés de l’API Microsoft Graph présentés ici constituent une partie de ce qui figure dans la documentation de référence sur l’API Microsoft Graph.

## <a name="register-a-management-application"></a>Inscrire une application de gestion

Avant qu’une application ou un script de gestion des utilisateurs que vous écrivez puisse interagir avec les ressources de votre locataire Azure AD B2C, vous avez besoin d’une inscription d’application qui accorde les autorisations nécessaires.

Suivez les étapes de cet article de procédure pour créer une inscription d’application que votre application de gestion peut utiliser :

[Gérer Azure AD B2C avec Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Opérations Microsoft Graph pour la gestion des utilisateurs

Les opérations suivantes de gestion des utilisateurs sont disponibles dans l’[API Microsoft Graph](/graph/api/resources/user) :

- [Obtenir une liste d’utilisateurs](/graph/api/user-list)
- [Créer un utilisateur](/graph/api/user-post-users)
- [Obtenir un utilisateur](/graph/api/user-get)
- [Mettre à jour un utilisateur](/graph/api/user-update)
- [Suppression d’un utilisateur](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Exemple de code : Comment gérer les comptes d’utilisateurs par programmation

Cet exemple de code est une application console .NET Core qui utilise le [SDK Microsoft Graph](/graph/sdks/sdks-overview) pour interagir avec l’API Microsoft Graph. Son code illustre comment appeler l’API pour gérer par programmation les utilisateurs dans un locataire Azure AD B2C.
Vous pouvez [télécharger l’archive de l’exemple](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), [parcourir le dépôt](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) sur GitHub ou cloner le dépôt :

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Une fois que vous avez obtenu l’exemple de code, configurez-le pour votre environnement, puis générez le projet :

1. Ouvrez le projet dans [Visual Studio](https://visualstudio.microsoft.com) ou dans [Visual Studio Code](https://code.visualstudio.com).
1. Ouvrez `src/appsettings.json`.
1. Dans la section `appSettings`, remplacez `your-b2c-tenant` par le nom de votre locataire, et `Application (client) ID` et `Client secret` par les valeurs de l’inscription de votre application de gestion (consultez la section [Inscrire une application de gestion](#register-a-management-application) de cet article).
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

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un index complet des opérations de l’API Microsoft Graph prises en charge pour les ressources Azure AD B2C, consultez [Opérations Microsoft Graph disponibles pour Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
