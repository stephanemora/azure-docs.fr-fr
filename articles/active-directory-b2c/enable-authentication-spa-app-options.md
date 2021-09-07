---
title: Activer les options d’application SPA avec Azure Active Directory B2C
description: Cet article décrit plusieurs façons d’activer l’utilisation des applications SPA.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 675db9c9d0a21b805ce8b4edeaff1a3b29ae7bfd
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525537"
---
# <a name="configure-authentication-options-in-a-single-page-application-by-using-azure-ad-b2c"></a>Configurer les options d’authentification dans une application monopage à l’aide d’Azure AD B2C

Cet article explique comment personnaliser et améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application monopage (SPA, single-page application). 

Avant de commencer, lisez l’article suivant : [Configurer l’authentification dans un exemple d’application web](configure-authentication-sample-spa-app.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Pour utiliser un domaine personnalisé et l’ID de votre locataire dans l’URL d’authentification, suivez les instructions fournies dans [Activer les domaines personnalisés](custom-domain.md). Recherchez votre objet de configuration MSAL et remplacez les valeurs de *authorities* et *knownAuthorities* pour utiliser votre nom de domaine et votre ID de locataire personnalisés.

Le code JavaScript suivant montre l’objet de configuration MSAL *avant* changement : 

```Javascript
const msalConfig = {
    auth: {
      ...
      authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_susi",
      knownAuthorities: ["fabrikamb2c.b2clogin.com"],
      ...
    },
  ...
}
```  

Le code JavaScript suivant montre l’objet de configuration MSAL *après* changement : 

```Javascript
const msalConfig = {
    auth: {
      ...
      authority: "https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi",
      knownAuthorities: ["custom.domain.com"],
      ...
    },
  ...
}
```  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée nécessaire, comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Créez un objet où stocker **login_hint**, puis passez cet objet à la méthode **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
        loginHint: "bob@contoso.com"
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Créez un objet où stocker **extraQueryParameters**, puis passez cet objet à la méthode **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {domain_hint: 'facebook.com'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation de la langue](language-customization.md).
1. Créez un objet où stocker **extraQueryParameters**, puis passez cet objet à la méthode **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {ui_locales: 'en-us'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configurez l’élément [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Créez un objet où stocker **extraQueryParameters**, puis passez cet objet à la méthode **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {campaignId: 'germany-promotion'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Dans votre stratégie personnalisée, définissez un [indicateur de jeton d’ID de profil technique](id-token-hint.md).
1. Créez un objet où stocker **extraQueryParameters**, puis passez cet objet à la méthode **MSAL loginPopup()** .

    ```javascript
    let loginRequest = {
         extraQueryParameters: {id_token_hint: 'id-token-hint-value'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

## <a name="secure-your-logout-redirect"></a>Sécuriser la redirection de déconnexion

Après la déconnexion, l’utilisateur est redirigé vers l’URI spécifié dans le paramètre `post_logout_redirect_uri`, quelles que soient les URL de réponse qui ont été spécifiées pour l’application. Cependant, si un `id_token_hint` valide est transmis et que l’option [Exiger un jeton d’ID dans les demandes de déconnexion](session-behavior.md#secure-your-logout-redirect) est activée, Azure AD B2C vérifie que la valeur de `post_logout_redirect_uri` correspond à l’un des URI de redirection configurés de l’application avant d’effectuer la redirection. Si aucune URL de réponse correspondante n’a été configurée pour l’application, un message d’erreur s’affiche et l’utilisateur n’est pas redirigé.

Pour prendre en charge un URI sécurisé de redirection après déconnexion, effectuez les étapes ci-dessous :

1. Créez une variable accessible globalement pour stocker le `id_token`.
    ```javascript
    let id_token = "";
    ```
    
1. Dans la fonction MSAL `handleResponse`, analysez le `id_token` de l’objet `authenticationResult` vers la variable `id_token`.
    ```javascript
    function handleResponse(response) {
        if (response !== null) {
            setAccount(response.account);
            id_token = response.idToken;
        } else {
            selectAccount();
        }
    }
    ```
    
1. Dans la section `signOut`, ajoutez le paramètre `id_token_hint` à l’objet **logoutRequest**.
    ```javascript
    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
            //set id_token_hint to the id_token value
            idTokenHint : id_token,
            mainWindowRedirectUri: msalConfig.auth.redirectUri
        };
        myMSALObj.logoutPopup(logoutRequest);
    }
    ```

Dans l’exemple ci-dessus, le **post_logout_redirect_uri** qui est passé dans la demande de déconnexion sera au format `https://your-app.com/`. Cette URL doit être ajoutée à l’URL de réponse de l’inscription d’application.

## <a name="enable-single-logout"></a>Activer le Single Logout

Dans Azure AD B2C, le Single Logout utilise la déconnexion front-channel OpenId Connect pour faire des demandes de déconnexion à toutes les applications auxquelles l’utilisateur s’est connecté via Azure AD B2C.

Ces demandes de déconnexion sont effectuées à partir de la page de déconnexion Azure AD B2C, dans un Iframe masqué. Les Iframes envoient des requêtes HTTP à tous les points de terminaison de déconnexion front-channel inscrits pour les applications qu’Azure AD B2C a enregistrées comme étant connectées. 

Le point de terminaison de déconnexion de chaque application doit appeler la méthode **MSAL logout()** . Dans ce scénario, vous devez également configurer explicitement MSAL pour qu’il s’exécute dans un Iframe. Pour cela, définissez `allowRedirectInIframe` sur `true`.

L’exemple de code suivant définit `allowRedirectInIframe` sur `true` :

```javascript
const msalConfig = {
    auth: {
        clientId: "enter_client_id_here",
        .....
    },
    cache: {
        cacheLocation: "..",
        ....
    },
    system: {
        allowRedirectInIframe: true
    };
}

async function logoutSilent(MSAL) {
   return MSAL.logout({
      onRedirectNavigate: (url) => {
         return false;
       }
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [options de configuration MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md).
