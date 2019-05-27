---
title: Application mobile par appels web API - appeler une API web | Plateforme d’identité Microsoft
description: Découvrez comment créer une application mobile qui appelle web API (en appelant une API web)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b6c203583a082228c2ba1f4c5f6fdb04d059be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962387"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Application mobile qui appelle des API - web appeler une API web

Une fois que votre application a un utilisateur connecté et reçu des jetons, MSAL expose plusieurs éléments d’information sur l’utilisateur, l’environnement de l’utilisateur et les jetons émis. Votre application peut utiliser ces valeurs pour appeler une API web ou afficher un message de Bienvenue à l’utilisateur.

Tout d’abord, nous allons examiner le résultat de la bibliothèque MSAL. Puis nous allons étudier comment utiliser un jeton d’accès à partir de la `AuthenticationResult` ou `result` pour appeler une API web protégée.

## <a name="msal-result"></a>Résultat de la bibliothèque MSAL
MSAL fournit les valeurs suivantes : 

- `AccessToken`: Utilisée pour appeler des API web protégées dans une requête HTTP porteur.
- `IdToken`: Contient des informations utiles sur l’utilisateur connecté, telles que le nom d’utilisateur, le locataire de base et un identificateur unique pour le stockage.
- `ExpiresOn`: Le délai d’expiration du jeton. MSAL gère l’actualisation automatique pour les applications.
- `TenantId`: L’identificateur du client, dont l’utilisateur connecté à l’aide. Pour les utilisateurs invités (Azure Active Directory B2B), cette valeur identifie le locataire que l’utilisateur connecté avec pas le client de base de l’utilisateur.  
- `Scopes`: Les étendues qui ont été accordées avec votre jeton. Les étendues accordés peuvent être un sous-ensemble des étendues que vous avez demandé.

MSAL fournit également une abstraction pour un `Account`. Un `Account` représente le compte de connexion de l’utilisateur actuel.

- `HomeAccountIdentifier`: Identificateur du locataire de base de l’utilisateur.
- `UserName`: Nom d’utilisateur par défaut de l’utilisateur. Cela peut être vide pour les utilisateurs Azure Active Directory B2C.
- `AccountIdentifier`: L’identificateur de l’utilisateur connecté. Cette valeur doit être le même que le `HomeAccountIdentifier` valeur dans la plupart des cas, sauf si l’utilisateur est un invité dans un autre client.

## <a name="call-an-api"></a>Appeler une API

Une fois que vous avez le jeton d’accès, il est facile d’appeler une API web. Votre application utilisera le jeton pour construire une requête HTTP, puis exécutez la requête.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Création de plusieurs demandes d’API

Si vous avez besoin d’appeler la même API plusieurs fois, ou si vous avez besoin d’appeler plusieurs API, prendre les éléments suivants en considération lorsque vous générez votre application :

- **Consentement incrémentiel**: Plateforme d’identité Microsoft permet aux applications d’obtenir le consentement de l’utilisateur que les autorisations sont requises, et non tout au début. Chaque fois que votre application est prête à appeler une API, il doit demander uniquement les étendues qu'elle doit utiliser.
- **Accès conditionnel**: Dans certains scénarios, vous pouvez obtenir les exigences d’accès conditionnel supplémentaires lorsque vous effectuez plusieurs requêtes d’API. Cela peut se produire si la première requête n’a aucune stratégie d’accès conditionnel appliquées et que votre application essaye d’accéder en mode silencieux une nouvelle API qui nécessite l’accès conditionnel. Pour gérer ce scénario, veillez à intercepter les erreurs des requêtes en mode silencieux et préparez-vous à effectuer une requête interactive.  Pour plus d’informations, consultez [des conseils pour l’accès conditionnel](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-mobile-production.md)
