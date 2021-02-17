---
title: Appeler une API web à partir d’une application démon | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une application démon appelant une API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bd0d53049c68843a6fd2cb6128c473d7c4f8d639
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582789"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Application démon appelant des API web - appeler une API web à partir de l’application

Les applications démon .NET peuvent appeler une API web. Les applications démon .NET peuvent également appeler plusieurs API web pré-approuvées.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Appeler une API web à partir d'une application démon

Pour appeler une API à l'aide du jeton, procédez comme suit :

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

Pour les applications de démon, les API web que vous appelez doivent être pré-approuvées. Il n'y a pas de consentement incrémentiel avec les applications démon. (Il n'y a aucune interaction avec l'utilisateur.) L'administrateur client doit donner son consentement à l'avance pour l'application et toutes les autorisations d'API. Si vous souhaitez appeler plusieurs API, acquérez un jeton pour chaque ressource, en appelant `AcquireTokenForClient` à chaque fois. MSAL utilisera le cache de jetons d’application afin d’éviter les appels de service inutiles.

## <a name="next-steps"></a>Étapes suivantes

# <a name="net"></a>[.NET](#tab/dotnet)

Passez à l’article suivant de ce scénario, [Passer en production](./scenario-daemon-production.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Passez à l’article suivant de ce scénario, [Passer en production](./scenario-daemon-production.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Passez à l’article suivant de ce scénario, [Passer en production](./scenario-daemon-production.md?tabs=java).

---
