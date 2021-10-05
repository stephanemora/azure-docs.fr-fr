---
title: Implémenter un contrôle d’accès en fonction du rôle dans les applications
titleSuffix: Microsoft identity platform
description: Découvrez comment implémenter le contrôle d’accès en fonction du rôle dans vos applications.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/17/2021
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: ccbdbde261914e95470e37b0e2046a95e1fe5898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602683"
---
# <a name="implement-role-based-access-control-in-apps"></a>Implémenter un contrôle d’accès en fonction du rôle dans les applications

Le contrôle d’accès en fonction du rôle (RBAC) permet aux utilisateurs ou groupes de disposer d’autorisations spécifiques concernant les ressources auxquelles ils ont accès, ce qu’ils peuvent faire avec ces ressources et qui gère quelles ressources.

En règle générale, lorsque nous parlons d’implémentation de RBAC pour protéger une ressource, nous cherchons à protéger une application web, une application monopage (SPA) ou une API.  Il peut s’agir de l’ensemble de l’application ou de l’API, ou de zones, fonctionnalités ou méthodes d’API spécifiques.

Cet article explique comment implémenter le contrôle d’accès en fonction du rôle propre à l’application.  Pour plus d'informations sur les bases des autorisations, consultez [Notions de base des autorisations](./authorization-basics.md).

## <a name="implementing-rbac-using-the-microsoft-identity-platform"></a>Implémentation de RBAC à l’aide de la plateforme d’identités Microsoft

Comme indiqué dans [Contrôle d’accès en fonction du rôle pour les développeurs d’applications](./custom-rbac-for-developers.md), il existe trois moyens d’implémenter le RBAC à l’aide de la plateforme d’identités Microsoft :

- **Rôles d’application** : utilisation de la [fonctionnalité Rôles d’application dans un enregistrement d’application](./howto-add-app-roles-in-azure-ad-apps.md#declare-roles-for-an-application) en association avec la logique au sein de votre application pour interpréter les attributions de rôles d’application entrantes.
- **Groupes** : utilisation des attributions de groupe d’une identité entrante en association avec la logique au sein de votre application pour interpréter les attributions de groupe. 
- **Magasin de données personnalisé** : récupérer et interpréter les attributions de rôles à l’aide de la logique au sein de votre application.

Il est recommandé d’utiliser la fonctionnalité *Rôles d’application*, car elle est plus facile à implémenter. Cette approche est prise en charge directement par les kits de développement logiciel (SDK) utilisés pour la création d’application à l’aide de la plateforme d’identités Microsoft. Pour obtenir davantage d’informations sur le choix d’une approche, consultez [Choix de l’approche](./custom-rbac-for-developers.md#choosing-an-approach).

Le reste de cet article vous montre comment définir des rôles d’application et implémenter le RBAC dans votre application à l’aide des rôles d’application.

## <a name="defining-roles-for-your-application"></a>Définition de rôles pour votre application

La première étape pour l’implémentation du RBAC pour votre application consiste à définir les rôles dont votre application a besoin, et à attribuer des utilisateurs ou des groupes à ces rôles.  Ce processus est décrit dans [Procédure : Ajouter des rôles d’application dans votre application et les recevoir dans le jeton](./howto-add-app-roles-in-azure-ad-apps.md). Une fois que vous avez défini vos rôles et les utilisateurs ou groupes attribués, vous pouvez accéder aux attributions de rôle dans les jetons arrivant dans votre application et agir sur ceux-ci en conséquence.

## <a name="implementing-rbac-in-aspnet-core"></a>Implémentation du RBAC dans ASP.NET Core 

ASP.NET Core prend en charge l’ajout de RBAC à une application web ASP.NET Core ou une API web.  Cela permet d’implémenter facilement le RBAC à l’aide des [vérifications de rôle](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks) avec l’attribut ASP.NET Core *Authorize*. Il est également possible d’utiliser la prise en charge d’ASP.NET Core pour la [vérification des rôles basée sur des stratégies](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#policy-based-role-checks).

### <a name="aspnet-core-mvc-web-application"></a>Application web ASP.NET Core MVC 

L’implémentation du RBAC dans une application web ASP.NET Core MVC est simple.  Elle implique essentiellement l’utilisation de l’attribut *Authorize* pour spécifier les rôles qui doivent être autorisés à accéder à des contrôles ou des actions spécifiques dans les contrôleurs. Suivez ces étapes pour implémenter le RBAC dans votre application ASP.NET Core MVC :
1. Créez une inscription d’application avec des rôles et des attributions d’application, comme indiqué dans *Définition de rôles pour votre application* ci-dessus.
1. Effectuez l’une des étapes suivantes :
    - Créez un projet d’application web ASP.NET Core MVC à l’aide de la commande de **CLI dotnet**.  Spécifiez l’indicateur *--auth* avec *SingleOrg* pour l’authentification à locataire unique ou *MultiOrg* pour l’authentification multi-locataire, l’indicateur *--client-id* avec le client s’il provient de l’inscription de votre application, et l’indicateur *--tenant-id* avec votre locataire s’il provient de votre locataire Azure AD :
 
      ```bash 
    
      dotnet new mvc --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID>  
    
      ```
      
    - Ajoutez les bibliothèques the Microsoft.Identity.Web et Microsoft.Identity.Web.UI à un projet ASP.NET Core MVC existant :
 
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Microsoft.Identity.Web.UI 

      ```

   Ensuite, suivez les instructions spécifiées dans [Guide de démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true) pour ajouter l’authentification à votre application.
1. Ajoutez les vérifications de rôles sur les actions des contrôleurs comme décrit dans [Ajout de vérifications de rôles](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks).
1. Testez l’application en tentant d’accéder à l’un des itinéraires MVC protégés.

### <a name="aspnet-core-web-api"></a>API web ASP.NET Core

L’implémentation du RBAC dans une API web ASP.NET Core implique essentiellement l’utilisation de l’attribut *Authorize* pour spécifier les rôles qui doivent être autorisés à accéder à des contrôles ou des actions spécifiques dans les contrôleurs. Suivez ces étapes pour implémenter le RBAC dans votre API web ASP.NET Core :
1. Créez une inscription d’application avec des rôles et des attributions d’application, comme indiqué dans *Définition de rôles pour votre application* ci-dessus.
1. Effectuez l’une des étapes suivantes :
    - Créez un projet d’API web ASP.NET Core MVC à l’aide de la commande de **CLI dotnet**.  Spécifiez l’indicateur *--auth* avec *SingleOrg* pour l’authentification à locataire unique ou *MultiOrg* pour l’authentification multi-locataire, l’indicateur *--client-id* avec le client s’il provient de l’inscription de votre application, et l’indicateur *--tenant-id* avec votre locataire s’il provient de votre locataire Azure AD :

      ```bash 
    
      dotnet new webapi --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID> 
    
      ```

    - Ajoutez les bibliothèques Microsoft.Identity.Web et Swashbuckle.AspNetCore à un projet d’API web ASP.NET Core existant :
      
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Swashbuckle.AspNetCore 

      ```
    
   Ensuite, suivez les instructions spécifiées dans [Guide de démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true) pour ajouter l’authentification à votre application.
1. Ajoutez les vérifications de rôles sur les actions des contrôleurs comme décrit dans [Ajout de vérifications de rôles](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks).
1. Appelez l’API depuis une application cliente.  Consultez [Application monopage Angular appelant une API web .NET Core et utilisant les Rôles d’application pour implémenter le contrôle d’accès en fonction du rôle](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles) pour obtenir un exemple complet.


## <a name="implementing-rbac-in-other-platforms"></a>Implémentation du RBAC dans d’autres plateformes

### <a name="angular-spa-using-msalguard"></a>SPA Angular utilisant MsalGuard
L’implémentation du RBAC dans une SPA Angular implique l’utilisation de [msal-angular](https://www.npmjs.com/package/@azure/msal-angular) pour autoriser l’accès aux itinéraires Angular contenus dans l’application.  Cela est illustré dans l’exemple [Permettre à votre application monopage Angular de connecter des utilisateurs et d’appeler des API avec la plateforme d’identités Microsoft](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial#chapter-5-control-access-to-your-protected-api-using-app-roles-and-security-groups).

> [!NOTE]
> Les implémentations de RBAC côté client doivent être associées au RBAC côté serveur pour empêcher les applications non autorisées d’accéder aux ressources sensibles.

### <a name="nodejs-with-express-application"></a>Application Node.js avec Express
L’implémentation du RBAC dans une application Node.js avec Express implique l’utilisation de MSAL pour autoriser l’accès aux itinéraires Express contenus dans l’application.  Cela est illustré dans l’exemple [Permettre à votre application web Node.js de connecter des utilisateurs et d’appeler des API avec la plateforme d’identités Microsoft](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial#chapter-4-control-access-to-your-app-using-app-roles-and-security-groups).

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [autorisations et le consentement dans la plateforme d’identités Microsoft](./v2-permissions-and-consent.md).
- Apprenez-en davantage sur le [contrôle d’accès en fonction du rôle pour les développeurs d’applications](./custom-rbac-for-developers.md).
