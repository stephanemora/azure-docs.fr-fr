---
title: Scénarios d’authentification pour la Plateforme d’identités Microsoft | Azure
description: Découvrez les flux d’authentification et scénarios d’applications pour la Plateforme d’identités Microsoft. Découvrez les différents types d’applications qui peuvent authentifier des identités, acquérir des jetons et appeler des API protégées.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5330111e5ae56471d26ebc39dca1a036246945e1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348583"
---
# <a name="authentication-flows-and-application-scenarios"></a>Flux d’authentification et scénarios d’applications

Le point de terminaison de la plateforme d’identité Microsoft v2.0 prend en charge l’authentification pour plusieurs architectures d’application modernes, toutes basées sur des protocoles industriels standard [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md).  Les applications se servent des [bibliothèques d’authentification](reference-v2-libraries.md) pour authentifier les identités et acquérir des jetons pour accéder aux API protégées. Cet article décrit les différents flux d’authentification et les scénarios d’applications dans lesquels ils sont utilisés.  Cet article fournit également des listes de [scénarios d’applications et flux d’authentification](#scenarios-and-supported-authentication-flows) et des [scénarios d’applications avec les plateformes et langages pris en charge](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Catégories d’applications

Des jetons peuvent être acquis à partir de plusieurs types d’applications : applications web, applications mobiles ou de bureau, API web et applications s’exécutant sur des appareils dépourvus de navigateur (ou iOT). Les applications peuvent être catégorisées comme suit :

- [Ressources protégées et applications clientes](#protected-resources-vs-client-applications). Certains scénarios concernent la protection de ressources (Web Apps ou API web), tandis que d’autres concernent l’acquisition d’un jeton de sécurité pour appeler une API web protégée.
- [Avec utilisateurs ou sans utilisateurs](#with-users-or-without-users). Certains scénarios impliquent un utilisateur connecté, tandis que d’autres n’impliquent pas d’utilisateur (scénarios de démon).
- [Applications monopages, applications clientes publiques et applications clientes confidentielles](#single-page-applications-public-client-applications-and-confidential-client-applications). Il existe trois grandes catégories de types d’applications. Les bibliothèques et objets utilisés pour les manipuler diffèrent.
- [Audience de connexion](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). Certains flux d’authentification ne sont pas disponibles pour certaines audiences de connexion. Certains flux sont disponibles uniquement pour les comptes professionnels ou scolaires, et d’autres le sont pour les comptes professionnels ou scolaires, ainsi que les comptes Microsoft personnels. L’audience autorisée dépend des flux d’authentification.
- [Flux OAuth 2.0 pris en charge](#scenarios-and-supported-authentication-flows).  Les flux d’authentification sont utilisés pour implémenter les scénarios d’applications qui demandent des jetons.  Il n’existe pas de mappage un-à-un entre des scénarios d’applications et des flux d’authentification.
- [Plateformes prises en charge](#scenarios-and-supported-platforms-and-languages). Tous les scénarios d’applications ne sont pas disponibles pour toutes les plateformes.

### <a name="protected-resources-vs-client-applications"></a>Ressources protégées et applications clientes

Les scénarios d’authentification impliquent deux activités :

- **Acquisition de jetons de sécurité** pour une API web protégée. Microsoft recommande d’utiliser des [bibliothèques d’authentification](reference-v2-libraries.md#microsoft-supported-client-libraries) pour acquérir des jetons, en particulier la famille de bibliothèques d’authentification Microsoft (MSAL)
- **Protection d’une API web** (ou d’une application web). L’un des défis de la protection d’une ressource (application web ou API web) est de valider le jeton de sécurité. Microsoft propose sur certaines plateformes des [bibliothèques d’intergiciels](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Avec utilisateurs ou sans utilisateurs

La plupart des scénarios d’authentification acquièrent des jetons pour le compte d’un **utilisateur** (connecté).

![Scénarios avec utilisateurs](media/scenarios/scenarios-with-users.svg)

Toutefois, il existe également des scénarios (applications démon) où les applications acquièrent des jetons pour elles-mêmes (sans utilisateur).

![applications démon](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>Applications monopages, applications clientes publiques et applications clientes confidentielles

Les jetons de sécurité peuvent être acquis à partir de plusieurs types d’applications. On peut diviser les applications en trois catégories :

- **Les applications monopages** (SPA) sont une catégorie d’applications web où les jetons sont acquis à partir de l’application en cours d’exécution dans le navigateur (écrite en JavaScript ou TypeScript). De nombreuses applications modernes disposent d’un frontend d’application à page unique écrit principalement en JavaScript. Souvent, l’application est écrite à l’aide d’une infrastructure telle qu’Angular, React ou Vue. MSAL.js est la seule bibliothèque d’authentification Microsoft prenant en charge les applications monopages.

- Les **applications clientes publiques** connectent toujours des utilisateurs. Ces applications sont :
  - Des applications de bureau appelant des API web au nom de l’utilisateur connecté.
  - Des applications mobiles.
  - Une troisième catégorie d’applications s’exécutant sur des appareils dépourvus de navigateur (par exemple, applications sans navigateur s’exécutant sur l’iOT).

  Elles sont représentées par la classe MSAL nommée [PublicClientApplication](msal-client-applications.md).

- **Applications clientes confidentielles**
  - Applications web appelant une API web
  - API web appelant une API web
  - Applications démon (même implémentées en tant que service de console tel qu’ un démon sur Linux ou un service Windows)
 
  Ces types d’applications utilisent la classe [ConfidentialClientApplication](msal-client-applications.md)

## <a name="application-scenarios"></a>Scénarios d’application

Le point de terminaison de la Plateforme d’identités Microsoft prend en charge l’authentification de diverses architectures d’application : applications monopages, applications web, API web, applications mobiles et natives, démons et applications côté serveur.  Les applications utilisent les différents flux d’authentification pour connecter les utilisateurs et obtenir des jetons pour appeler des API protégées.

### <a name="single-page-application"></a>Application monopage

De nombreuses applications web modernes sont créées en tant qu’applications monopages côté client et écrites avec JavaScript ou une infrastructure de type SPA comme Angular, Vue.js et React.js. Ces applications s’exécutent dans un navigateur web et présentent des caractéristiques d’authentification différentes de celles des applications web côté serveur traditionnelles. La plateforme des identités Microsoft permet aux applications monopages de connecter des utilisateurs et d’obtenir des jetons pour accéder aux services back-end ou aux API web.

![Application monopage](media/scenarios/spa-app.svg)

Pour plus d’informations, consultez [Applications monopages](scenario-spa-overview.md).

### <a name="web-application-signing-in-a-user"></a>Application web connectant un utilisateur

![Une application web connecte des utilisateurs](media/scenarios/scenario-webapp-signs-in-users.svg)

Pour **protéger une application** web (connectant l’utilisateur), vous allez utiliser :

- Dans le monde .NET, ASP.NET ou ASP.NET Core avec l’intergiciel Open ID Connect ASP.NET. En coulisses, la protection d’une ressource implique la validation du jeton de sécurité qui est effectuée par la bibliothèque d’[extensions IdentityModel pour .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki), non par les bibliothèques MSAL.

- Si vous développez node. js, vous allez utiliser Passport.js.

Pour plus d’informations, consultez [Application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md).

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Application web connectant un utilisateur et appelant une API web au nom de l’utilisateur

![Une application web appelle des API web](media/scenarios/web-app.svg)

À partir de l’application web, pour **appeler l’API web** au nom de l’utilisateur, utilisez la classe `ConfidentialClientApplication` de MSAL. Vous allez utiliser le flux de code d’autorisation, qui stocke le jeton acquis dans le cache de jeton. Le contrôleur acquiert ensuite les jetons en mode silencieux à partir du cache si nécessaire. MSAL actualise le jeton si nécessaire.

Pour plus d’informations, consultez [Une application web appelle des API web](scenario-web-app-call-api-overview.md).

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>Application de bureau appelant une API web au nom de l’utilisateur connecté

Pour appeler une API web à partir d’une application de bureau qui connecte des utilisateurs, utilisez les méthodes d’acquisition de jetons interactives de la classe PublicClientApplication de MSAL. Ces méthodes interactives vous permettent de contrôler l’expérience de l’interface utilisateur de connexion. Pour permettre cette interaction, MSAL utilise un navigateur web.

![Bureau](media/scenarios/desktop-app.svg)

Pour les applications hébergées sur Windows qui s’exécutent sur des ordinateurs joints à un domaine Windows ou joints à AAD, il existe une autre possibilité. Ces applications peuvent acquérir un jeton en mode silencieux à l’aide de l’[authentification Windows intégrée](https://aka.ms/msal-net-iwa).

Les applications qui s’exécutent sur un appareil sans navigateur peuvent toujours appeler une API pour le compte d’un utilisateur. Pour s’authentifier, l’utilisateur doit se connecter sur un autre appareil doté d’un navigateur web. Pour permettre ce scénario, vous devez utiliser le [flux de code de l’appareil](https://aka.ms/msal-net-device-code-flow)

![Flux de code d’appareil](media/scenarios/device-code-flow-app.svg)

Pour finir, bien que ce ne soit pas recommandé, vous pouvez utiliser un [Nom d’utilisateur/Mot de passe](https://aka.ms/msal-net-up) dans les applications clientes publiques. Ce flux reste nécessaire dans certains scénarios (comme DevOps), mais il faut savoir que son utilisation peut imposer des contraintes à votre application. Par exemple, les applications qui utilisent ce flux ne peuvent pas connecter un utilisateur qui doit effectuer une authentification multifacteur (accès conditionnel). Ce flux ne permet pas non plus à votre application de bénéficier de l’authentification unique. L’authentification avec nom d’utilisateur/mot de passe va à l’encontre des principes de l’authentification moderne et n’est fournie que pour des raisons d’héritage.

Dans les applications de bureau, si vous souhaitez que le cache de jeton soit persistant, vous devez [personnaliser la sérialisation du cache de jeton](https://aka.ms/msal-net-token-cache-serialization). Vous pouvez même activer des caches de jeton offrant une compatibilité descendante et ascendante avec des générations précédentes de bibliothèques d’authentification (ADAL.NET 3.x et 4.x) en implémentant une [sérialisation du cache à double jeton](https://aka.ms/msal-net-dual-cache-serialization).

Pour plus d’informations, consultez [Application de bureau qui appelle des API web](scenario-desktop-overview.md).

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>Application mobile appelant une API web au nom de l’utilisateur qui s’est connecté de manière interactive

Comme pour les applications de bureau, une application mobile utilisera les méthodes d’acquisition de jetons interactives de la classe PublicClientApplication de MSAL pour acquérir un jeton afin d’appeler une API web.

![Mobile](media/scenarios/mobile-app.svg)

MSAL iOS et MSAL Android, par défaut, utilisent le navigateur web du système. Toutefois, vous pouvez aussi leur donner pour instruction d’utiliser l’affichage web incorporé. Il existe des spécificités en fonction de la plateforme mobile : (UWP, iOS, Android).

Certains scénarios, impliquant un accès conditionnel lié à l’ID d’appareil ou à l’inscription de l’appareil, nécessitent l’installation d’un [répartiteur](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) sur un appareil. Le portail d’entreprise Microsoft (sur Android) et Microsoft Authenticator (Android et iOS) sont des exemples de répartiteurs. MSAL est désormais en capable d’interagir avec des répartiteurs.

> [!NOTE]
> Des stratégies de protection des applications peuvent être appliquées  à votre application mobile (utilisant MSAL. iOS, MSAL.Android ou MSAL.NET/Xamarin), par exemple, pour empêcher les utilisateurs de copier du texte protégé. L’application est [gérée par Intune](https://docs.microsoft.com/intune/app-sdk) et reconnue par Intune en tant qu’application gérée. Le [Kit de développement logiciel (SDK) Intune](https://docs.microsoft.com/intune/app-sdk-get-started) est séparé des bibliothèques MSAL et communique avec AAD de façon autonome.

Pour plus d’informations, consultez [Application mobile qui appelle des API web](scenario-mobile-overview.md).

### <a name="protected-web-api"></a>API web protégée

Vous pouvez utiliser le point de terminaison de la plateforme d’identité Microsoft pour sécuriser des services web, comme l’API web RESTful de votre application. Une API web protégée est appelée avec un jeton d’accès pour sécuriser ses données et authentifier les demandes entrantes. L’appelant d’une API web ajoute un jeton d’accès dans l’en-tête d’autorisation d’une requête HTTP. Si vous souhaitez protéger votre API web ASP.NET ou ASP.NET Core, vous devez valider le jeton d’accès. Pour ce faire, vous allez utiliser l’intergiciel JWT ASP.NET. En coulisses, la validation est effectuée par la bibliothèque d’[extensions IdentityModel pour .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki), non par MSAL.NET

Pour plus d’informations, consultez [API web protégée](scenario-protected-web-api-overview.md).

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>API web appelant une autre API web en aval au nom de l’utilisateur pour qui elle a été appelée

Si, en outre, vous voulez que votre API web protégée ASP.NET ou ASP.NET Core appelle une autre API web au nom de l’utilisateur, l’application doit acquérir un jeton pour l’API web en aval en utilisant la méthode de la classe ConfidentialClientApplication Acquisition de jeton [pour le compte d’un utilisateur](https://aka.ms/msal-net-on-behalf-of). On parle également alors d’appels de service à service.
Les API web appelant une autre API web doivent également fournir une sérialisation de cache personnalisée

  ![API Web](media/scenarios/web-api.svg)

Pour plus d’informations, consultez [API web qui appelle des API web](scenario-web-api-call-api-overview.md).

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>Application de bureau/service ou démon web appelant une API web sans utilisateur (en son propre nom)

Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans interaction utilisateur doivent également disposer d’un moyen d’accès aux API web sécurisées. Ces applications peuvent s’authentifier et récupérer des jetons à l’aide de l’identité d’application plutôt qu’avec l’identité déléguée d’un utilisateur. Elles prouvent leur identité à l’aide d’une clé secrète client ou d’un certificat.
Vous pouvez écrire de telles applications (applications démon) acquérant un jeton pour l’application en plus d’utiliser les méthodes d’acquisition d’[informations d’identification du client](https://aka.ms/msal-net-client-credentials) de la classe ConfidentialClientApplication de MSAL. Cela suppose que l’application a précédemment inscrit une clé secrète (mot de passe d’application, certificat ou assertion du client) auprès d’Azure AD, qu’elle partage ensuite avec cet appel.

![Application démon](media/scenarios/daemon-app.svg)

Pour plus d’informations, consultez [Application démon qui appelle des API web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scénarios et flux d’authentification pris en charge

Les scénarios qui impliquent l’acquisition de jetons mappent également à des flux d’authentification OAuth 2.0 décrits en détail dans [Protocoles de la Plateforme d’identités Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scénario</th> <th>Procédure pas à pas de scénario détaillé</th> <th>Flux/Octroi OAuth 2.0</th> <th>Audience</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Application à page unique</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicite</a></td>
   <td>Comptes professionnels ou scolaires et comptes personnels, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Application web qui connecte les utilisateurs</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Code d’autorisation</a></td>
   <td>Comptes professionnels ou scolaires et comptes personnels, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Application web qui appelle des API web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Code d’autorisation</a></td>
   <td>Comptes professionnels ou scolaires et comptes personnels, B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Application de bureau qui appelle des API web</a></td>
   <td>Interactif (<a href="v2-oauth2-auth-code-flow.md">code d’autorisation</a> avec PKCE)</td>
   <td>Comptes professionnels ou scolaires et comptes personnels, B2C</td>
 </tr>

  <tr>
   <td>Authentification Windows intégrée</td>
   <td>Comptes professionnels ou scolaires</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Mot de passe de propriétaire de la ressource</a></td>
   <td>Comptes professionnels ou scolaires, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Code d’appareil</a></td>
   <td>Comptes professionnels ou scolaires*</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Application mobile qui appelle des API web</a></td>
   <td>Interactif (<a href="v2-oauth2-auth-code-flow.md">code d’autorisation</a> avec PKCE)</td>
   <td>Comptes professionnels ou scolaires et comptes personnels, B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Mot de passe de propriétaire de la ressource</a></td>
   <td>Comptes professionnels ou scolaires, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls Web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">Application démon qui appelle des API web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Informations d’identification du client</a></td>
   <td>Autorisations d’application uniquement (pas d’utilisateur) uniquement sur des organisations AAD</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">API web qui appelle des API web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Au nom de</a></td>
   <td>Comptes professionnels ou scolaires et comptes personnels</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scénarios avec les plateformes et langages pris en charge

Tous les types d’application ne sont pas disponibles sur toutes les plateformes. Vous pouvez également utiliser différents langages pour générer vos applications. Les bibliothèques d’authentification Microsoft prennent en charge un certain nombre de **plateformes** (JavaScript, .NET Framework, .NET Core, Windows 10/UWP, Xamarin.iOS, Xamarin.Android, iOS natif, Mac OS, Android natif, Java, Python). Dans le tableau ci-dessous, sur Windows, chaque fois que .NET Core est mentionné, le .NET Framework est également possible (il est omis pour ne pas encombrer le tableau).

|Scénario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Application à page unique](scenario-spa-overview.md) <br/>[![Application monopage](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md) <br/>[![Application web qui connecte les utilisateurs](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Application web qui appelle des API web](scenario-web-app-call-api-overview.md) <br/> <br/>[![Application web qui appelle des API web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Application de bureau qui appelle des API web](scenario-desktop-overview.md) <br/> <br/>[![Application de bureau qui appelle des API web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![Flux de code de l’appareil](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> MSAL.objc |
| [Application mobile qui appelle des API web](scenario-mobile-overview.md) <br/> [![Application mobile qui appelle des API web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C ou swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [Application démon](scenario-daemon-overview.md) <br/> [![Application démon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [API web qui appelle des API web](scenario-web-api-call-api-overview.md) <br/><br/> [![API web qui appelle des API web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Consultez également [Bibliothèques prises en charge par Microsoft selon le système d’exploitation/language](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur les [notions de base de l’authentification](authentication-scenarios.md) et les [jetons d’accès](access-tokens.md).
