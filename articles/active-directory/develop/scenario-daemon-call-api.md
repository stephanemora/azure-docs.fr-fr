---
title: Appeler une API web à partir d’une application démon - Plateforme d’identités Microsoft | Azure
description: Découvrez comment générer une application démon appelant des API web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46f1676ab1bdcf2b23907824bb9bf543b5f28ce6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962608"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Application démon appelant des API web - appeler une API web à partir de l’application

Une application démon peut appeler une API web à partir d’une application démon .NET ou appeler plusieurs API web pré-approuvées.

## <a name="calling-a-web-api-daemon-application"></a>Appel une application démon d’API web

Voici comment utiliser le jeton pour appeler une API :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Appels de plusieurs API

Pour les applications de démon, les API web que vous appelez doivent être pré-approuvées. Il n’y aura pas de consentement incrémentiel avec des applications démons (il n’y a aucune interaction utilisateur). L’administrateur locataire doit approuver l’application et toutes les autorisations d’API au préalable. Si vous souhaitez appeler plusieurs API, vous aurez besoin d’acquérir un jeton pour chaque ressource, en appelant `AcquireTokenForClient` à chaque fois. MSAL utilisera le cache de jetons d’application afin d’éviter les appels de service inutiles.

## <a name="next-steps"></a>Étapes suivantes

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Application démon - déplacement en production](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Application démon - déplacement en production](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Application démon - déplacement en production](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
