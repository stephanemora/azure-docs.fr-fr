---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7e9518f8a90faa0566b96d58992b01e4b0a642f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202720"
---
## <a name="test-your-code"></a>Test de votre code

Pour tester votre application dans Visual Studio, appuyez sur **F5** afin d’exécuter votre projet. Le navigateur s’ouvre sur l’emplacement http://<span></span>localhost:{port} et vous voyez le bouton **Se connecter avec Microsoft**. Sélectionnez le bouton pour démarrer le processus de connexion.

Lorsque vous êtes prêt pour le test, utilisez un compte Microsoft Azure Active Directory (compte professionnel ou scolaire) ou un compte Microsoft personnel (<span>live.</span>com ou <span>outlook.</span>com) pour vous connecter.

![Se connecter avec Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Connexion à votre compte Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Afficher les résultats de l’application
Une fois connecté, l’utilisateur est redirigé vers la page d’accueil de votre site web. La page d’accueil correspond à l’URL HTTPS qui est spécifiée dans les informations d’inscription de votre application, dans le portail d’inscription des applications de Microsoft. La page d’accueil comprend le message de bienvenue *« Bonjour \<Utilisateur>, »* un lien pour se déconnecter et un lien pour afficher les revendications de l’utilisateur. Le lien des revendications de l’utilisateur redirige vers le contrôleur *Claims* que vous avez créé précédemment.

### <a name="browse-to-see-the-users-claims"></a>Accéder aux revendications de l’utilisateur
Pour afficher les revendications de l’utilisateur, sélectionnez le lien pour accéder à la vue de contrôleur qui est disponible uniquement pour les utilisateurs authentifiés.

#### <a name="view-the-claims-results"></a>Afficher les résultats des revendications
Une fois que vous avez accédé à la vue de contrôleur, vous devez voir un tableau qui contient les propriétés de base de l’utilisateur :

|Propriété |Valeur |Description |
|---|---|---|
|**Name** |Nom complet de l’utilisateur | Prénom et nom de l’utilisateur
|**Nom d’utilisateur** |utilisateur<span>@domain.com</span> | Nom d’utilisateur employé pour identifier l’utilisateur.
|**Objet** |Objet |Chaîne qui identifie de manière unique l’utilisateur sur le web.|
|**Tenant ID** |Guid | **GUID** qui représente de manière unique l’organisation Azure AD de l’utilisateur.|

En outre, vous devez voir un tableau avec toutes les revendications qui se trouvent dans la requête d’authentification. Pour plus d’informations, consultez la [liste des revendications qui se trouvent dans un jeton d’ID AD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Tester l’accès à une méthode disposant d’un attribut Authorize (facultatif)
Pour tester l’accès en tant qu’utilisateur anonyme à un contrôleur protégé avec l’attribut `Authorize`, procédez comme suit :
1. Sélectionnez le lien de déconnexion de l’utilisateur et terminez le processus de déconnexion.
2. Dans le navigateur, tapez http://<span></span>localhost:{port}/claims pour accéder à votre contrôleur qui est protégé par l’attribut `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Résultats attendus après l’accès à un contrôleur protégé
Vous êtes invité à vous authentifier pour utiliser la vue de contrôleur protégé.

## <a name="advanced-options"></a>Options avancées

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Protéger l’ensemble de votre site web
Pour protéger tout votre site web, dans le fichier **Global.asax**, ajoutez l’attribut `AuthorizeAttribute` au filtre `GlobalFilters` dans la méthode `Application_Start` :

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restreindre les utilisateurs autorisés à se connecter à votre application
Par défaut, lorsque vous générez l’application créée par ce guide, votre application accepte des connexions de comptes personnels (y compris outlook.com, live.com et autres), ainsi que des comptes professionnels et scolaires de n’importe quelle société ou organisation ayant intégré Azure Active Directory. Il s’agit d’une option recommandée pour les applications SaaS.

Pour restreindre les utilisateurs qui peuvent se connecter à votre application, plusieurs options sont disponibles :

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Option 1 : limiter la connexion à votre application aux utilisateurs de l’instance Active Directory d’une seule organisation (un seul locataire)

Cette option est un scénario courant pour les *applications métier* : si vous souhaitez que votre application accepte les connexions uniquement à partir des comptes qui appartiennent à une instance Azure Active Directory spécifique (y compris les *comptes invités* de cette instance), procédez comme suit :

1. Dans le fichier **web.config**, remplacez la valeur du paramètre `Tenant` de `Common` par le nom du locataire de l’organisation, par exemple `contoso.onmicrosoft.com`.
2. Dans la classe [OWIN Startup](#configure-the-authentication-pipeline), définissez l’argument `ValidateIssuer` sur `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Option 2 : restreindre l’accès à votre application aux utilisateurs figurant dans une liste spécifique d’organisations

Vous pouvez limiter les connexions aux comptes d’utilisateurs d’une organisation Azure AD figurant dans la liste des organisations autorisées :
1. Dans la classe [OWIN Startup](#configure-the-authentication-pipeline), définissez l’argument `ValidateIssuer` sur `true`.
2. Définissez la valeur du paramètre `ValidIssuers` sur la liste des organisations autorisées.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Option 3 : utiliser une méthode personnalisée pour valider les émetteurs
Vous pouvez implémenter une méthode personnalisée pour valider les émetteurs à l’aide du paramètre **IssuerValidator**. Pour plus d’informations sur l’utilisation de ce paramètre, lisez cette rubrique MSDN concernant la [classe TokenValidationParameters](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx).

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
