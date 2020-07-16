---
title: Authentifier une application cliente
titleSuffix: Azure Digital Twins
description: Découvrez comment authentifier une application cliente auprès du service Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390820"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Authentifier une application cliente avec Azure Digital Twins

Après avoir [créé une instance Azure Digital Twins](how-to-set-up-instance.md), vous pouvez créer une application cliente que vous allez utiliser pour interagir avec l’instance. Après avoir configuré un projet client de démarrage, cet article vous montre comment authentifier correctement cette application cliente avec l’instance Azure Digital Twins.

Cette opération comprend deux étapes :
1. Créer une inscription d’application
2. Écrire un code d’authentification dans une application cliente

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Créer une inscription d’application

Pour vous authentifier auprès d’Azure Digital Twins à partir d’une application cliente, vous devez configurer une **inscription d’application** dans [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Cette inscription d’application est l’emplacement où vous configurez les autorisations d’accès aux [API d’Azure Digital Twins](how-to-use-apis-sdks.md). Votre application cliente s’authentifie auprès de l’inscription de l’application, les autorisations d’accès configurées sont alors accordées aux API.

Pour créer une inscription d’application, vous devez fournir les ID de ressource aux API d’Azure Digital Twins et les autorisations de base pour l’API. Dans votre répertoire de travail, ouvrez un nouveau fichier et saisissez l’extrait de code JSON suivant pour configurer les informations suivantes : 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Enregistrez ce fichier sous *manifest.json*.

> [!NOTE] 
> Il existe certains endroits où une chaîne « conviviale », lisible `https://digitaltwins.azure.net` peut être utilisée pour l’ID d’application de ressource Azure Digital Twins à la place du GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0`. Par exemple, de nombreux exemples dans cette documentation utilisent l’authentification avec la bibliothèque MSAL, et la chaîne conviviale peut être utilisée pour cela. Toutefois, au cours de cette étape de création de l’inscription de l’application, la forme GUID de l’ID est obligatoire, comme indiqué ci-dessus. 

Dans la fenêtre de Cloud Shell, cliquez sur l’icône « Charger/Télécharger des fichiers », puis choisissez « Charger ».

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Fenêtre Cloud Shell présentant la sélection de l’option de chargement":::
Accédez au fichier *manifest.JSON* que vous venez de créer et appuyez sur « Ouvrir ».

Ensuite, exécutez la commande suivante pour créer une inscription d’application (en remplaçant les espaces réservés si nécessaire) :

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

La sortie de cette commande ressemble à ceci.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Nouvelle inscription d’application AAD":::

Une fois l’inscription d’application créée, suivez [ce lien](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) pour accéder à la page de présentation de l’inscription d’application AAD dans le portail Azure.

Dans cette vue d’ensemble, sélectionnez l’inscription d’application que vous venez de créer dans la liste. Les détails s’affichent alors dans une page similaire à celle-ci :

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Portail Azure : ID d’authentification":::

Prenez note de *l’ID d’application (client)* et de *l’ID de répertoire (locataire)* affichés sur **votre** page. Vous utiliserez ces valeurs ultérieurement pour authentifier une application cliente sur les API Azure Digital Twins.

> [!NOTE]
> Selon votre scénario, vous devrez peut-être apporter des modifications supplémentaires à l’inscription d’application. Voici quelques-unes des exigences courantes que vous devrez peut-être rencontrer :
> * Activer l’accès client public
> * Définir des URL de réponse spécifiques pour l’accès web et au bureau
> * Autoriser les flux d’authentification OAuth2 implicites
> * Si votre abonnement Azure est créé à l’aide d’un compte Microsoft tel que Live, Xbox ou Hotmail, vous devez définir *signInAudience* sur l’inscription d’application pour prendre en charge les comptes personnels.
> Le moyen le plus simple de configurer ces paramètres consiste à utiliser le [portail Azure](https://portal.azure.com/). Pour plus d’informations concernant ce processus, consultez [Inscrire une application auprès de la plateforme d’identités Microsoft](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Écrire le code d’authentification de l’application cliente : kit de développement logiciel .NET (C#)

Cette section décrit le code que vous devez inclure dans votre application cliente afin d’effectuer le processus d’authentification à l’aide du kit de développement logiciel .NET (C#).
Le kit de développement logiciel C# Azure Digital Twins fait partie du kit de développement logiciel Azure pour .NET. Il se trouve ici : [Bibliothèque de client Azure IoT Digital Twins pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore de projet d’application cliente de démarrage, créez un projet .NET de base à utiliser avec ce didacticiel.

Pour pouvoir utiliser le kit de développement logiciel .NET, vous devez inclure les packages suivants dans votre projet :
* `Azure.DigitalTwins.Core` (version `1.0.0-preview.2`)
* `Azure.Identity`

Selon les outils que vous choisissez, vous pouvez procéder à la configuration à l’aide du gestionnaire de package Visual Studio ou de l’outil en ligne de commande `dotnet`. 

### <a name="authentication-and-client-creation-net"></a>Authentification et création du client : .NET

Pour vous authentifier avec le kit de développement logiciel .NET, utilisez l’une des méthodes d’obtention d’informations d’identification définies dans la bibliothèque [Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet).

En voici deux couramment utilisées : 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Cette méthode est destinée aux applications interactives et affiche un navigateur Web pour l’authentification.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Cette méthode fonctionne très bien dans les cas où vous avez besoin [d’identités managées (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), en cas d’utilisation de Azure Functions par exemple. 

Vous aurez également besoin des instructions using suivantes :

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

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

Dans une fonction Azure, vous pouvez ensuite utiliser les informations d’identification de l’identité managée comme suit :

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Découvrez [comment faire : Configurez une fonction Azure pour le traitement des données](how-to-create-azure-function.md) pour obtenir un exemple plus complet sur quelques-unes des options de configuration importantes dans le contexte des fonctions.

En outre, pour utiliser l’authentification dans une fonction, n’oubliez pas :
* [Activer une identité managée](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Variables d’environnement](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Affectez des autorisations à l’application de fonction pour lui permettre d’accéder aux API Digital Twins. Découvrez [comment faire : Configurer une fonction Azure pour le traitement des données](how-to-create-azure-function.md) pour de plus amples informations.

## <a name="authentication-in-an-autorest-generated-sdk"></a>Authentification dans un kit de développement logiciel généré par AutoRest

Si vous n’utilisez pas .NET, vous pouvez choisir de créer une bibliothèque SDK dans le langage de votre choix, comme décrit dans [comment faire : Créer des SDK personnalisés pour Azure Digital Twins avec AutoRest](how-to-create-custom-sdks.md).

Cette section explique comment s’authentifier dans un tel cas.

### <a name="prerequisites"></a>Prérequis

Cet exemple utilise un SDK typescript généré avec autorest. Par conséquent, il requiert également les éléments suivants :
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Exemple de code d’authentification minimal

Pour authentifier une application .NET avec des services Azure, vous pouvez utiliser le code minimal suivant au sein de votre application cliente.

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

MSAL offre de nombreuses autres options que vous pouvez utiliser pour implémenter des éléments comme la mise en cache et d’autres flux d’authentification. Pour plus d’informations, consultez [Vue d’ensemble de Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le fonctionnement de la sécurité dans Azure Digital Twins :
* [Concepts : Sécurité pour les solutions Azure Digital Twins](concepts-security.md)

Sinon, maintenant que l’authentification est configurée, passez à la création de modèles dans votre instance :
* [Guide pratique pour Gérer les modèles personnalisés](how-to-manage-model.md)