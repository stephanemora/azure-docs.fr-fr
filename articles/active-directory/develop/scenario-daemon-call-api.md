---
title: Application démon conçue pour appeler des API web (appel des API web) - Plateforme d’identités Microsoft
description: Apprendre à générer une application démon qui appelle des API web (appel des API web)
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eacb574f20abeb63a9d0ab8caf534eb7abb9784
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056363"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Application démon appelant des API web - appeler une API web à partir de l’application

Une application démon peut appeler une API web à partir d’une application démon .NET ou appeler plusieurs API web pré-approuvées.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Appeler une API web à partir d’une application démon .NET

Voici comment utiliser le jeton pour appeler une API

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

> [!div class="nextstepaction"]
> [Application démon - déplacement en production](./scenario-daemon-production.md)