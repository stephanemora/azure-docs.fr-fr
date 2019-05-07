---
title: Application mobile par appels web API - appeler une API web | Plateforme d’identité Microsoft
description: Découvrez comment créer une application mobile qui appelle des API Web (en appelant une API Web)
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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075114"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Application mobile qui appelle des API - web appeler une API web

Une fois que votre application a un utilisateur connecté et reçu des jetons, MSAL expose plusieurs éléments d’informations sur l’utilisateur, leur environnement et les jetons émis. Votre application peut utiliser ces valeurs pour appeler une API web ou afficher un message de Bienvenue à l’utilisateur.

Tout d’abord, nous allons examiner le résultat de la bibliothèque MSAL, puis comment utiliser un jeton d’accès à partir de la `AuthenticationResult` ou `result` pour appeler une API web protégée.

## <a name="msal-result"></a>Résultat de la bibliothèque MSAL

- `AccessToken`: Utilisée pour appeler des API web protégées dans une demande du porteur de HTTP.
- `IdToken`: Contient des revendications utiles relatives à l’utilisateur connecté, comme leur nom, le locataire de base et l’identificateur unique pour le stockage.
- `ExpiresOn`: le délai d’expiration du jeton. MSAL gère l’actualisation automatique pour les applications.
- `TenantId`: L’identificateur de client de l’utilisateur utilisé pour la connexion. Pour les utilisateurs invités (Azure AD B2B), ce sera le locataire de l’utilisateur connecté avec pas leur locataire de base.  
- `Scopes`: les étendues qui ont été accordées avec votre jeton. Cela peut être un sous-ensemble de ce qui vous est demandé.

En outre, MSAL fournit également une abstraction pour un `Account`. Un compte représente l’utilisateur actuel connecté dans le compte.

- `HomeAccountIdentifier`: Identificateur du locataire de base de l’utilisateur.
- `UserName`: Nom d’utilisateur privilégié de l’utilisateur. Cela peut être vide pour les utilisateurs Azure AD B2C.
- `AccountIdentifier`: Identificateur de l’utilisateur connecté. Cela sera le même que le `HomeAccountIdentifier` dans la plupart des cas, sauf si l’utilisateur est un invité dans un autre client.

## <a name="calling-an-api"></a>Appel d’une API

Une fois que vous avez le jeton d’accès prêt, il est simple d’appeler une API web. Votre application sera prenons ce jeton, construire une requête HTTP et exécutez-le.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

        // Put Access token in HTTP Request
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

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Création de plusieurs demandes d’API

Si vous devez appeler la même API plusieurs fois ou plusieurs API, il existe des considérations supplémentaires lors de la création de votre application :

- ***Consentement incrémentiel***: Plateforme d’identité Microsoft permet aux applications d’obtenir le consentement de l’utilisateur comme autorisation sont requis, plutôt que tous les initiaux. Chaque fois que votre application est prête à appeler une API, il doit demander uniquement les étendues qu'il envisage d’utiliser.
- ***Accès conditionnel***: Dans certains scénarios, vous pouvez obtenir les exigences d’accès conditionnel supplémentaires lors de l’établissement de plusieurs requêtes d’API. Pour gérer ce scénario, veillez à intercepter les erreurs des requêtes en mode silencieux et préparez-vous à effectuer une requête interactive. Cela peut se produire si la première requête n’a aucune stratégie d’accès conditionnel appliquées et que votre application essaye d’accéder en mode silencieux une nouvelle API qui nécessite l’accès conditionnel. Pour plus d’informations, consultez [des conseils pour l’accès conditionnel](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-mobile-production.md)
