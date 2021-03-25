---
title: Incorporer l’interface utilisateur Azure Active Directory B2C dans votre application avec une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Découvrez comment incorporer l’interface utilisateur Azure Active Directory B2C dans votre application avec une stratégie personnalisée
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4fbb09a2687673ef96252d8bdb0523d0c5f235ae
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772023"
---
# <a name="embedded-sign-in-experience"></a>Expérience de connexion incorporée

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Pour simplifier l’expérience de connexion, vous pouvez éviter de rediriger les utilisateurs vers une page de connexion distincte ou de générer une fenêtre contextuelle. L’élément de cadre en ligne `<iframe>` vous permet d’incorporer l’interface utilisateur de connexion Azure AD B2C directement dans votre application web.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Connexion incorporée à une application web

L’élément de cadre en ligne `<iframe>` permet d’incorporer un document dans une page web HTML5. Vous pouvez utiliser l’élément IFrame pour incorporer l’interface utilisateur de connexion Azure AD B2C directement dans votre application web, comme illustré dans l’exemple suivant :

![Connexion avec l’expérience DIV de survol](media/embedded-login/login-hovering.png)

Lors de l’utilisation d’un IFrame, tenez compte de ce qui suit :

- Une connexion incorporée ne prend en charge que des comptes locaux. La plupart des fournisseurs d’identité sociale (par exemple, Google et Facebook) bloquent l’affichage de leurs pages de connexion dans des cadres en ligne.
- Étant donné que les cookies de session Azure AD B2C dans un IFrame sont considérés comme des cookies tiers, certains navigateurs (par exemple, Safari ou Chrome en mode Incognito) les bloquent ou les suppriment, ce qui entraîne une expérience utilisateur indésirable. Pour éviter ce problème, assurez-vous que le nom de domaine de votre application et votre domaine Azure AD B2C ont la *même origine*. Pour utiliser la même origine, [activez les domaines personnalisés](custom-domain.md) pour le locataire Azure AD B2C, puis configurez votre application web avec la même origine. Par exemple, une application hébergée sur https://app.contoso.com a la même origine qu’Azure AD B2C s’exécutant sur https://login.contoso.com .

## <a name="prerequisites"></a>Prérequis

* Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](custom-policy-get-started.md).
* [Activez les domaines personnalisés](custom-domain.md) pour vos stratégies.

## <a name="configure-your-policy"></a>Configurer votre stratégie

Pour permettre l’incorporation de votre interface utilisateur Azure AD B2C dans un IFrame, une stratégie de sécurité de contenu `Content-Security-Policy` et des options de cadre `X-Frame-Options` doivent être incluses dans les en-têtes de réponse HTTP Azure AD B2C. Ces en-têtes permettent à l’interface utilisateur Azure AD B2C de s’exécuter sous le nom de domaine de votre application.

Ajoutez un élément **JourneyFraming** à l’intérieur de l’élément [RelyingParty](relyingparty.md).  L’élément **UserJourneyBehaviors** doit suivre immédiatement l’élément **DefaultUserJourney**. Votre élément **UserJourneyBehaviors** devrait ressembler à l’exemple suivant :

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

L’attribut **Sources** contient l’URI de votre application web. Ajoutez une espace entre les URI. Chaque URL doit répondre aux exigences suivantes :

- L’URI doit être approuvé et détenu par votre application.
- L’URI doit utiliser le schéma HTTPS.  
- L’URI complet de l’application web doit être spécifié. Les caractères génériques ne sont pas pris en charge.

De plus, nous vous recommandons de bloquer l’incorporation de votre propre nom de domaine dans un IFframe en définissant les en-têtes Content-Security-Policy et X-Frame-Options sur les pages de votre application. Cela a pour effet d’atténuer les problèmes de sécurité liés à des navigateurs plus anciens concernant l’incorporation imbriquée d’IFrames.

## <a name="adjust-policy-user-interface"></a>Ajuster l’interface utilisateur de la stratégie

Avec [personnalisation de l’interface utilisateur](customize-ui.md) Azure AD B2C, vous disposez d’un contrôle presque total sur le contenu HTML et CSS présenté aux utilisateurs. Suivez les étapes pour personnaliser une page HTML à l’aide de définitions de contenu. Pour adapter l’interface utilisateur Azure AD B2C à la taille de l’IFrame, fournissez une page HTML vierge sans arrière-plan ou espaces supplémentaires.  

Le code CSS suivant masque les éléments HTML Azure AD B2C et ajuste la taille du panneau de manière à remplir l’IFrame.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

Dans certains cas, vous pouvez souhaiter informer votre application de la page Azure AD B2C actuellement présentée. Par exemple, quand un utilisateur sélectionne l’option d’inscription, vous pouvez souhaiter que l’application réponde en masquant les liens de connexion avec un compte de réseau social, ou en ajustant la taille de l’IFrame.

Pour notifier votre application de la page Azure AD B2C actuelle, [activez votre stratégie pour JavaScript](./javascript-and-page-layout.md), puis utilisez des messages de publication HTML5. Le code JavaScript suivant envoie un message de publication à l’application avec `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Configurer une application web

Quand un utilisateur sélectionne le bouton de connexion, l’[application web](code-samples.md#web-apps-and-apis) génère une demande d’autorisation qui amène l’utilisateur à expérience de connexion Azure AD B2C. Une fois la connexion établie, Azure AD B2C retourne un jeton d’ID ou un code d’autorisation à l’URI de redirection configuré dans votre application.

Pour prendre en charge la connexion incorporée, la propriété **src** de l’IFrame pointe vers le contrôleur de connexion, tel `/account/SignUpSignIn`, qui génère la demande d’autorisation et redirige l’utilisateur vers une stratégie Azure AD B2C.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Une fois que l’application a reçu et validé le jeton d’ID, le flux d’autorisation est terminé et l’application reconnaît et approuve l’utilisateur. Étant donné que le flux d’autorisation se produit à l’intérieur de l’IFrame, vous devez recharger la page principale. Une fois la page rechargée, le bouton de connexion devient « se déconnecter » et le nom d’utilisateur est présenté dans l’interface utilisateur.  

Voici un exemple montrant comment l’URI de redirection de connexion peut actualiser la page principale :

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Ajouter la connexion avec des comptes de réseaux sociaux à une application web

Des fournisseurs d’identité sociale bloquent l’affichage de leurs pages de connexion dans des cadres en ligne. Vous pouvez utiliser une stratégie distincte pour les comptes de réseaux sociaux, ou une stratégie unique pour la connexion et l’inscription avec des comptes locaux et de réseaux sociaux. Vous pouvez ensuite utiliser le paramètre de chaîne de requête `domain_hint`. Le paramètre d’indication du domaine dirige l’utilisateur directement vers la page de connexion du fournisseur d’identité sociale.

Dans votre application, ajoutez la connexion avec des boutons de compte de réseau social. Quand un utilisateur clique sur l’un des boutons de compte de réseau social, le contrôle doit modifier le nom de la stratégie ou définir le paramètre d’indication du domaine.

<!-- TBD: add a diagram -->

L’URI de redirection peut être le même que celui qu’utilise l’IFrame. Vous pouvez ignorer le rechargement de la page.

## <a name="configure-a-single-page-application"></a>Configurer une application à page unique

Pour une application à page unique, vous devez également disposer d’une deuxième page HTML de connexion qui se charge dans l’IFrame. Cette page de connexion héberge le code de la bibliothèque d’authentification qui génère le code d’autorisation et retourne le jeton.

Quand l’application à page unique a besoin du jeton d’accès, utilisez un code JavaScript pour obtenir le jeton d’accès à partir de l’IFrame et de l’objet qui le contient.

> [!NOTE]
> L’exécution de MSAL 2.0 dans un IFrame n’est pas prise en charge actuellement.

Le code suivant est un exemple qui s’exécute sur la page principale et appelle le code JavaScript d’un IFrame :

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles associés suivants :

- [Personnalisation de l’interface utilisateur](customize-ui.md)
- Référence de l’élément [RelyingParty](relyingparty.md)
- [Activer votre stratégie pour JavaScript](./javascript-and-page-layout.md)
- [Exemples de code](code-samples.md)

::: zone-end
