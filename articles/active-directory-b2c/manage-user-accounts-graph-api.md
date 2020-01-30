---
title: Utiliser l’API Graph dans Azure Active Directory B2C
description: Explique comment gérer les utilisateurs dans un locataire Azure AD B2C en appelant l’API Azure AD Graph et en utilisant une identité d’application pour automatiser le processus.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a9e55edcb7c107a3dfa91f61aaa1fea64bc62f21
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76851056"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C : Utiliser l’API Azure AD Graph

Les locataires Azure Active Directory B2C (Azure AD B2C) peuvent avoir des milliers ou des millions d’utilisateurs. Par conséquent, de nombreuses tâches courantes de gestion de client doivent être effectuées par programmation. La gestion des utilisateurs en est un parfait exemple.

Il se peut que vous ayez besoin de migrer un magasin d’utilisateurs existant vers un client B2C ou que vous souhaitiez héberger l’inscription des utilisateurs sur votre page et créer des comptes d’utilisateur dans votre répertoire Azure AD B2C en arrière-plan. Pour effectuer ces genres de tâches, vous devez être en mesure de créer, de lire, de mettre à jour et de supprimer des comptes d’utilisateur. Vous pouvez effectuer ces tâches avec l’API Azure AD Graph.

Pour les locataires B2C, il existe deux modes principaux de communication avec l’API Graph :

* Pour les tâches **interactives** à exécution unique, vous devez agir en tant que compte d’administrateur dans le locataire B2C quand vous exécutez ces tâches. Avec ce mode, un administrateur doit se connecter à l’aide de ses informations d’identification avant de pouvoir effectuer des appels à l’API Graph.
* Pour les tâches **automatisées**, en continu, vous devez utiliser un compte de service doté des privilèges nécessaires pour effectuer des tâches de gestion. Dans Azure AD, vous pouvez le faire en inscrivant une application et en l’authentifiant auprès d’Azure AD à l’aide d’un *ID d’application* qui utilise [l’octroi des informations d’identification du client OAuth 2.0](../active-directory/develop/service-to-service.md). Dans ce cas, l’application joue son propre rôle, et non celui d’un utilisateur, pour appeler l’API Graph.

Dans cet article, vous découvrirez comment exécuter le cas d’usage automatisé. Vous allez créer un `B2CGraphClient` .NET 4.5 qui effectuera les opérations de création, de lecture, de mise à jour et de suppression (CRUD) d’utilisateurs. Le client aura une interface de ligne de commande (CLI) Windows permettant d’appeler des méthodes différentes. Toutefois, le code est écrit pour se comporter de façon non interactive et automatisée.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de pouvoir créer des applications ou des utilisateurs, vous devez disposer d’un locataire Azure AD B2C. Si vous n’en avez pas, [Créez un locataire Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Inscrire une application

Une fois que vous avez un locataire Azure AD B2C, vous devez inscrire votre application de gestion à l’aide du [portail Azure](https://portal.azure.com). Vous devez également lui accorder les autorisations nécessaires pour effectuer des tâches de gestion pour le compte de votre application de gestion ou script automatisé.

### <a name="register-application-in-azure-active-directory"></a>Inscrire une application dans Azure Active Directory

Pour utiliser l’API Azure AD Graph avec votre locataire B2C, vous devez inscrire une application à l’aide du workflow d’inscription d’application Azure Active Directory.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Affecter des autorisations d’accès à l’API

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Créer un secret client

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Vous disposez maintenant d’une application autorisée à *créer*, *lire* et *mettre à jour* des utilisateurs dans votre locataire Azure AD B2C. Passez à la section suivante pour ajouter des autorisations de *suppression* d’utilisateurs et de *mise à jour des mots de passe*.

## <a name="add-user-delete-and-password-update-permissions"></a>Ajouter des autorisations de suppression d’utilisateurs et de mise à jour des mots de passe

L’autorisation *Accéder en lecture et en écriture aux données de l’annuaire* que vous avez accordée plus tôt n’inclut **PAS** la possibilité de supprimer des utilisateurs ou de mettre à jour leurs mots de passe.

Si vous souhaitez donner à votre application la possibilité de supprimer des utilisateurs ou de mettre à jour des mots de passe, vous devez lui accorder le rôle *Administrateur d’utilisateurs*.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Rôles et administrateurs**.
1. Sélectionnez le rôle **Administrateur d’utilisateurs**.
1. Sélectionnez **Ajouter une attribution**.
1. Dans la zone de texte **Sélectionner**, entrez le nom de l’application que vous avez inscrite, par exemple *managementapp1*. Sélectionnez votre application quand elle apparaît dans les résultats de la recherche.
1. Sélectionnez **Ajouter**. La propagation complète des autorisations peut prendre quelques minutes.

Votre application Azure AD B2C dispose désormais des autorisations supplémentaires requises pour supprimer des utilisateurs ou mettre à jour leurs mots de passe dans votre locataire B2C.

## <a name="get-the-sample-code"></a>Obtention de l'exemple de code

L’exemple de code est une application console .NET qui utilise la bibliothèque [ADAL (Active Directory Authentication Library)](../active-directory/develop/active-directory-authentication-libraries.md) pour interagir avec l’API Azure AD Graph. Son code illustre comment appeler l’API pour gérer par programmation les utilisateurs dans un locataire Azure AD B2C.

Vous pouvez [télécharger l’exemple d’archive](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*.zip) ou cloner le dépôt GitHub :

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Une fois que vous avez obtenu l’exemple de code, configurez-le pour votre environnement, puis générez le projet :

1. Ouvrez la solution `B2CGraphClient\B2CGraphClient.sln` dans Visual Studio.
1. Dans le projet **B2CGraphClient**, ouvrez le fichier *App.config*.
1. Remplacez la section `<appSettings>` par le code XML suivant. Remplacez ensuite `{your-b2c-tenant}` par le nom de votre locataire, et `{Application ID}` et `{Client secret}` par les valeurs que vous avez enregistrées précédemment.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Générez la solution. Cliquez avec le bouton droit sur la solution **B2CGraphClient** dans l’Explorateur de solutions, puis sélectionnez **Regénérer la solution**.

Si la génération réussit, l’application console `B2C.exe` se trouve dans `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>Réviser l’exemple de code

Pour utiliser B2CGraphClient, ouvrez une invite de commandes (`cmd.exe`) et accédez au répertoire `Debug` du projet. Exécutez ensuite la commande `B2C Help`.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

La commande `B2C Help` affiche une brève description des sous-commandes disponibles. À chaque fois que vous appelez l’une de ses sous-commandes, `B2CGraphClient` envoie une requête à l’API Azure AD Graph.

Les sections suivantes décrivent comment le code de l’application effectue des appels à l’API Azure AD Graph.

### <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

Toute requête envoyée à l’API Azure AD Graph nécessite un jeton d’accès pour l’authentification. `B2CGraphClient` utilise la bibliothèque ADAL (Active Directory Authentication Library) open source pour vous aider à obtenir des jetons d’accès. La bibliothèque ADAL facilite l’acquisition des jetons en fournissant une API d’assistance et en prenant soin de certains détails importants, tels que la mise en cache des jetons d’accès. Cependant, vous n’êtes pas obligé d’utiliser la bibliothèque ADAL pour obtenir des jetons. Vous pouvez à la place obtenir des jetons en créant manuellement des requêtes HTTP.

> [!NOTE]
> Vous devez utiliser ADAL v2 ou version ultérieure pour obtenir des jetons d’accès qui peuvent être utilisés avec l’API Azure AD Graph. Vous ne pouvez pas utiliser ADAL v1.

Quand `B2CGraphClient` s’exécute, il crée une instance de la classe `B2CGraphClient`. Le constructeur de cette classe définit la structure d’authentification de la bibliothèque ADAL :

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Nous allons utiliser la commande `B2C Get-User` à titre d’exemple.

Quand `B2C Get-User` est appelée sans arguments supplémentaires, l’application appelle la méthode `B2CGraphClient.GetAllUsers()`. `GetAllUsers()` appelle ensuite `B2CGraphClient.SendGraphGetRequest()`, qui envoie une requête HTTP GET à l’API Azure AD Graph. Avant d’envoyer la requête GET, `B2CGraphClient.SendGraphGetRequest()` obtient un jeton d’accès à l’aide de la bibliothèque ADAL :

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

Vous pouvez obtenir un jeton d’accès pour l’API Graph en appelant la méthode `AuthenticationContext.AcquireToken()` de la bibliothèque ADAL. La bibliothèque ADAL renvoie un `access_token` qui représente l’identité de l’application.

### <a name="read-users"></a>Lecture des utilisateurs

Pour obtenir la liste des utilisateurs ou un utilisateur particulier à partir de l’API Azure AD Graph, vous pouvez envoyer une requête HTTP `GET` au point de terminaison `/users`. Une requête pour obtenir tous les utilisateurs d’un client se présente ainsi :

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour afficher cette requête, exécutez :

 ```cmd
 B2C Get-User
 ```

Deux points importants sont à prendre en considération :

* Le jeton d’accès obtenu au moyen de la bibliothèque ADAL est ajouté à l’en-tête `Authorization` à l’aide du schéma `Bearer`.
* Pour les clients B2C, vous devez utiliser le paramètre de requête `api-version=1.6`.

Ces détails sont gérés dans la méthode `B2CGraphClient.SendGraphGetRequest()` :

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Création de comptes d’utilisateurs clients

Quand vous créez des comptes d’utilisateur dans votre locataire B2C, vous pouvez envoyer une requête HTTP `POST` au point de terminaison `/users`. La requête HTTP `POST` suivante montre un exemple d’utilisateur à créer dans le locataire.

La plupart des propriétés de la requête suivante sont requises pour créer des utilisateurs clients. Les commentaires `//` ont été inclus à des fins d’illustration : ne les incluez pas dans une requête réelle.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Pour afficher la requête, exécutez l’une des commandes suivantes :

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

La commande `Create-User` prend comme paramètre d’entrée un fichier JSON qui contient une représentation JSON d’un objet utilisateur. L’exemple de code contient deux exemples de fichiers JSON : `usertemplate-email.json` et `usertemplate-username.json`. Vous pouvez modifier ces fichiers en fonction de vos besoins. Outre les champs obligatoires ci-dessus, ces fichiers incluent plusieurs champs facultatifs.

Pour plus d’informations sur les champs obligatoires et facultatifs, consultez [Référence des entités et des types complexes | Référence de l’API Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Vous pouvez voir comment la requête POST est construite dans `B2CGraphClient.SendGraphPostRequest()` :

* attache un jeton d’accès à l’en-tête `Authorization` de la demande ;
* définit le paramètre `api-version=1.6`;
* inclut l’objet utilisateur JSON dans le corps de la requête.

> [!NOTE]
> Si les comptes que vous souhaitez migrer à partir d’un magasin d’utilisateurs existant ont un mot de passe moins fort que les [règles de mot de passe fort d’Azure AD B2C](user-flow-password-complexity.md), vous pouvez désactiver la condition de mot de passe fort en utilisant la valeur `DisableStrongPassword` dans la propriété `passwordPolicies`. Par exemple, vous pouvez modifier la requête de création d’utilisateur précédente comme suit : `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Mise à jour de comptes d’utilisateurs clients

Le processus de mise à jour d’objets utilisateur est similaire à celui utilisé pour créer des objets utilisateur, mais il utilise la méthode HTTP `PATCH` :

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Essayez de mettre à jour un utilisateur en modifiant certaines valeurs dans vos fichiers JSON, puis utilisez `B2CGraphClient` pour exécuter l’une des commandes suivantes :

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspectez la méthode `B2CGraphClient.SendGraphPatchRequest()` pour plus d’informations sur l’envoi de cette requête.

### <a name="search-users"></a>Rechercher des utilisateurs

Vous pouvez rechercher des utilisateurs dans votre tenant B2C de différentes façons :

* Référencez l’**ID d’objet** de l’utilisateur.
* Référencez leur identificateur de connexion, la propriété `signInNames`.
* Faites référence à l’un des paramètres OData valides (par exemple, « givenName », « Surname » ou « displayName »).

Exécutez l’une des commandes suivantes pour rechercher un utilisateur :

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Par exemple :

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Suppression d’utilisateurs

Pour supprimer des utilisateurs, utilisez la méthode HTTP `DELETE` et construisez l’URL avec l’ID d’objet de l’utilisateur :

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour obtenir un exemple, saisissez la commande suivante et consultez la requête de suppression affichée dans la console :

```cmd
B2C Delete-User <object-id-of-user>
```

Inspectez la méthode `B2CGraphClient.SendGraphDeleteRequest()` pour plus d’informations sur l’envoi de cette requête.

L’API Azure AD Graph permet d’effectuer de nombreuses actions en plus de la gestion des utilisateurs. La [référence de l’API Azure AD Graph](/previous-versions/azure/ad/graph/api/api-catalog) fournit des informations détaillées sur chaque action, ainsi que des exemples de demande.

## <a name="use-custom-attributes"></a>Utilisation d’attributs personnalisés

La plupart des applications grand public doivent stocker un certain type d’informations de profil utilisateur personnalisé. Pour ce faire, une solution consiste à définir un attribut personnalisé dans votre locataire B2C. Vous pouvez ensuite traiter cet attribut de la même façon que toute autre propriété d’un objet utilisateur. Vous pouvez mettre à jour l’attribut, le supprimer, l’interroger, l’envoyer en tant que revendication dans les jetons de connexion, etc.

Pour définir un attribut personnalisé dans votre client B2C, consultez la [référence d’attribut personnalisé dans B2C](user-flow-custom-attributes.md).

Vous pouvez voir les attributs personnalisés définis dans votre locataire B2C à l’aide des commandes `B2CGraphClient` suivantes :

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

La sortie révèle les détails de chaque attribut personnalisé. Par exemple :

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Vous pouvez utiliser le nom complet, tel que `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, en tant que propriété de vos objets utilisateur. Mettez à jour votre fichier JSON avec la nouvelle propriété et une valeur pour la propriété, puis exécutez :

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Étapes suivantes

Avec `B2CGraphClient`, vous disposez d’une application de service capable de gérer les utilisateurs de votre client B2C par programmation. `B2CGraphClient` utilise sa propre identité d’application pour s’authentifier auprès de l’API Azure AD Graph. et acquiert des jetons à l’aide d’une clé secrète client.

Quand vous intégrez cette fonctionnalité dans votre application, prenez en considération les quelques points clés suivants pour les applications B2C :

* Accordez à l’application les autorisations nécessaires dans le locataire.
* Lorsque vous appelez l’API Graph, utilisez `api-version=1.6`.
* Lorsque vous créez et mettez à jour des utilisateurs clients, certaines propriétés sont requises, comme décrit plus haut.
