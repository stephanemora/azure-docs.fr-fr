---
title: Écrire le code d’authentification de l’application
titleSuffix: Azure Digital Twins
description: Voir comment écrire un code d’authentification dans une application cliente
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 88f74bcc93d640ec8d4d9014c6f25a6d0d0df680
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614002"
---
# <a name="write-client-app-authentication-code"></a>Écrire le code d’authentification de l’application cliente

Après avoir [configuré une instance et une authentification Azure Digital Twins](how-to-set-up-instance-scripted.md), vous pouvez créer une application cliente que vous allez utiliser pour interagir avec l’instance. Après avoir configuré un projet client de démarrage, cet article vous montre **comment écrire du code dans cette application cliente pour l’authentifier** avec l’instance Azure Digital Twins.

Il y a deux approches de l’exemple de code dans cet article. Vous pouvez utiliser celui qui vous convient, en fonction du langage choisi :
* La première section de l’exemple de code utilise le kit de développement logiciel (SDK) Azure Digital Twins .NET (C#). Le kit de développement logiciel (SDK) fait partie du kit de développement logiciel (SDK) Azure pour .NET et se trouve ici : [*Bibliothèque de client Azure IoT Digital Twins pour .NET*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* La deuxième section de l’exemple de code est destinée aux utilisateurs qui n’utilisent pas le kit de développement logiciel (SDK) .NET, mais des kits de développement logiciel (SDK) générés par AutoRest dans d’autres langages. Pour plus d’informations sur cet stratégie, consultez [*Guide pratique : Créer des kits SDK personnalisés pour Azure Digital Twins avec AutoRest*](how-to-create-custom-sdks.md).

Vous pouvez également en apprendre plus sur les API et les kits de développement logiciel (SDK) pour Azure Digital Twins dans [*Guide pratique : Utiliser les API et les kits de développement logiciel (SDK) Azure Digital Twins*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Prérequis

Tout d’abord, suivez les étapes de configuration décrites dans [*Guide pratique : Configurer une instance et l’authentification*](how-to-set-up-instance-scripted.md). Cela permet de s’assurer que vous disposez d’une instance Azure Digital Twins, que votre utilisateur a des autorisations d’accès et que vous avez configuré des autorisations pour les applications clientes. Une fois cette configuration terminée, vous êtes prêt à écrire le code de l’application cliente.

Pour continuer, vous aurez besoin d’un projet d’application cliente dans lequel écrire votre code. Si vous n’avez pas encore configuré de projet d’application cliente, créez un projet de base à utiliser avec ce didacticiel dans le langage de votre choix.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Authentification et création du client : kit de développement logiciel (SDK) .NET (C#)

Tout d’abord, incluez les packages suivants dans votre projet afin d’utiliser le kit de développement logiciel (SDK) .NET et les outils d’authentification pour cette procédure :
* `Azure.DigitalTwins.Core`
* `Azure.Identity`

Selon les outils que vous choisissez, vous pouvez inclure les packages à l’aide du gestionnaire de package Visual Studio ou de l’outil en ligne de commande `dotnet`. 

Vous aurez également besoin des instructions using suivantes :

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
Pour vous authentifier avec le kit de développement logiciel .NET, utilisez l’une des méthodes d’obtention d’informations d’identification définies dans la bibliothèque [Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet). Voici deux des méthodes les plus couramment utilisées (même conjointement dans la même application) :

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) est destiné aux applications interactives et peut être utilisé pour créer un client de kit de développement logiciel (SDK) authentifié
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) fonctionne parfaitement lorsqu’il vous faut des identités managées (MSI) et peut être utilisé avec Azure Functions

### <a name="interactivebrowsercredential-method"></a>Méthode InteractiveBrowserCredential
La méthode [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) est destinée aux applications interactives et affiche un navigateur web pour l’authentification.

Pour utiliser les informations d’identification du navigateur interactif afin de créer un client de kit de développement logiciel authentifié, ajoutez ce code :

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Bien que vous puissiez placer l’ID client, l’ID de locataire et l’URL de l’instance directement dans le code comme indiqué ci-dessus, il peut être judicieux de faire en sorte que votre code récupère ces valeurs à partir d’un fichier de configuration ou d’une variable d’environnement.

### <a name="managedidentitycredential-method"></a>Méthode ManagedIdentityCredential
 La méthode [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) fonctionne parfaitement lorsqu’il vous faut des [identités managées (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), en cas d’utilisation d’Azure Functions, par exemple.
Dans une fonction Azure, vous pouvez utiliser les informations d’identification de l’identité managée comme suit :

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Consultez le [*Guide pratique : Configurer une fonction Azure pour le traitement des données*](how-to-create-azure-function.md) pour obtenir un exemple plus complet sur quelques-unes des options de configuration importantes dans le contexte des fonctions.

En outre, pour utiliser l’authentification dans une fonction, n’oubliez pas :
* [Activer une identité managée](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* D’utiliser des [variables d'environnement](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) le cas échéant
* Affectez des autorisations à l’application de fonction pour lui permettre d’accéder aux API Digital Twins. Pour plus d’informations sur les processus Azure Functions, consultez [*Guide pratique : Configurer une fonction Azure pour le traitement des données*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Authentification avec un kit de développement logiciel (SDK) généré par AutoRest

Si vous n’utilisez pas .NET, vous pouvez choisir de créer une bibliothèque SDK dans le langage de votre choix, comme décrit dans [*Guide pratique : Créer des kits SDK personnalisés pour Azure Digital Twins avec AutoRest*](how-to-create-custom-sdks.md).

Cette section explique comment s’authentifier dans un tel cas.

### <a name="prerequisites"></a>Prérequis

Tout d’abord, vous devez effectuer les étapes de création d’un kit de développement logiciel (SDK) personnalisé avec AutoRest, en suivant les étapes décrites dans [*Guide pratique : Créer des kits SDK personnalisés pour Azure Digital Twins avec AutoRest*](how-to-create-custom-sdks.md).

Cet exemple utilise un SDK typescript généré avec autorest. Par conséquent, il requiert également les éléments suivants :
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Exemple de code d’authentification minimal

Pour authentifier une application avec des services Azure, vous pouvez utiliser le code minimal suivant au sein de votre application cliente.

Vous aurez besoin de votre *ID d’application (client)* et de *l’ID de répertoire (locataire)* , ainsi que l’URL de votre instance Azure Digital Twins.

> [!TIP]
> L’URL de l’instance Azure Digital Twins est créée en ajoutant *https://* au début du *nom d’hôte* de votre instance Azure Digital Twins. Pour afficher le *nom d’hôte*, ainsi que toutes les propriétés de votre instance, vous pouvez exécuter `az dt show --dt-name <your-Azure-Digital-Twins-instance>`. Vous pouvez utiliser la commande `az account show --query tenantId` pour voir votre *ID de répertoire (locataire)* . 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Notez une fois encore que bien que le code ci-dessus place l’ID client, l’ID de locataire et l’URL de l’instance directement dans le code pour plus de simplicité, il peut être judicieux de faire en sorte que votre code récupère ces valeurs à partir d’un fichier de configuration ou d’une variable d’environnement.

MSAL offre de nombreuses autres options que vous pouvez utiliser pour implémenter des éléments comme la mise en cache et d’autres flux d’authentification. Pour plus d’informations, consultez [*Vue d’ensemble de Microsoft Authentication Library (MSAL)* ](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le fonctionnement de la sécurité dans Azure Digital Twins :
* [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md)

Sinon, maintenant que l’authentification est configurée, passez à la création de modèles dans votre instance :
* [*Guide pratique : Gérer des modèles personnalisés*](how-to-manage-model.md)