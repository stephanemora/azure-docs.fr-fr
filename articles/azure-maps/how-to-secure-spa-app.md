---
title: Guide pratique pour sécuriser une application web monopage à l’aide d’une connexion non interactive dans Microsoft Azure Maps
titleSuffix: Azure Maps
description: Guide pratique pour configurer une application web monopage avec un contrôle d’accès en fonction du rôle Azure (Azure RBAC) non interactif et le kit SDK web Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js, subject-rbac-steps
ms.openlocfilehash: 9bf18a9122bbe8406b76cfd822cc2a5a86339a52
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641226"
---
# <a name="how-to-secure-a-single-page-web-application-with-non-interactive-sign-in"></a>Guide pratique pour sécuriser une application web monopage à l’aide d’une connexion non interactive

Cet article explique comment sécuriser une application web monopage avec Azure Active Directory (Azure AD), lorsque l’utilisateur n’est pas en mesure de se connecter à Azure AD.

Pour créer ce flux d’authentification non interactif, nous allons créer un service web sécurisé Azure Function qui sera chargé de l’acquisition des jetons d’accès à partir d’Azure AD. Ce service web sera uniquement disponible pour votre application web monopage.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps peut prendre en charge les jetons d’accès à partir des flux interactifs ou d’authentification unique de l’utilisateur. Vous pouvez utiliser les flux interactifs pour une étendue plus limitée de révocation d’accès et de gestion des secrets.

## <a name="create-an-azure-function"></a>Création d’une fonction Azure

Pour créer une application de service web sécurisée, responsable de l’authentification auprès d’Azure AD :

1. Créez une fonction dans le portail Azure. Pour plus d’informations, consultez [Bien démarrer avec Azure Functions](../azure-functions/functions-get-started.md).

2. Configurez la stratégie CORS sur la fonction Azure pour que l’application web monopage puisse y accéder. La stratégie CORS sécurise l’accès des clients de navigateur aux origines autorisées de votre application web. Pour plus d’informations, consultez [Ajoutez des fonctionnalités CORS](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Ajoutez une identité affectée par le système](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) sur la fonction Azure afin d’autoriser la création d’un principal de service pour l’authentification auprès d’Azure AD.  

4. Accordez l’accès en fonction du rôle pour l’identité affectée par le système au compte Azure Maps. Pour plus d’informations, consultez [Accorder un accès en fonction du rôle](#grant-role-based-access-for-users-to-azure-maps).

5. Écrivez du code pour la fonction Azure afin d’obtenir des jetons d’accès Azure Maps à l’aide d’une identité affectée par le système avec l’un des mécanismes pris en charge ou le protocole REST. Pour plus d’informations, consultez [Obtenir des jetons pour les ressources Azure](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity).

    Voici un exemple de protocole REST :

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Et voici un exemple de réponse :

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Configurez la sécurité pour la fonction Azure HttpTrigger :

   1. [Créer une clé d’accès à une fonction](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   1. [Sécurisez le point de terminaison HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) pour la fonction Azure en production.

7. Configurez le kit SDK web Azure Maps d’une application web. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Étapes suivantes

Mieux comprendre un scénario d’application monopage :
> [!div class="nextstepaction"]
> [Application monopage](../active-directory/develop/scenario-spa-overview.md)

Recherchez les métriques d’utilisation de l’API pour votre compte Azure Maps :
> [!div class="nextstepaction"]
> [Afficher les métriques d’utilisation](how-to-view-api-usage.md)

Explorez d’autres exemples qui montrent comment intégrer Azure AD à Azure Maps :
> [!div class="nextstepaction"]
> [Exemples Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
