---
title: Tutoriel - Enregistrer une application pour activer l’inscription et la connexion à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Utilisez le portail Azure pour créer un locataire Azure AD B2C et enregistrer une application avec.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: patricka
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.openlocfilehash: 81ab3288d7a365c2665b3b38ca220a3e7cb648c7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>Tutoriel : enregistrer une application pour activer l’inscription et la connexion à l’aide d’Azure Active Directory B2C

Ce tutoriel vous aide à créer un locataire Microsoft Azure Active Directory (Azure AD) B2C et à enregistrer une application avec en quelques minutes.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement
> * Inscrivez votre application

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Créer un client Azure AD B2C

Les fonctionnalités B2C ne peuvent pas être activées dans vos locataires existants. Vous devez créer un locataire Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Félicitations, vous avez créé un locataire Azure Active Directory B2C. Vous êtes administrateur général du locataire. Vous pouvez ajouter d’autres administrateurs généraux en fonction des besoins. Pour basculer vers votre nouveau locataire, cliquez sur le *lien permettant de gérer votre nouveau locataire*.

![Lien permettant de gérer votre nouveau locataire](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Si vous envisagez d’utiliser un client B2C pour une application de production, consultez l’article sur [les clients de mise à l’échelle pour production/clients B2C de la version préliminaire](active-directory-b2c-reference-tenant-type.md). Il existe des problèmes connus liés à la suppression d’un locataire B2C existant et à sa recréation sous le même nom de domaine. Vous devez créer un locataire B2C portant un nom de domaine différent.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Lier votre locataire à votre abonnement

Vous devez lier votre locataire Azure AD B2C à votre abonnement Azure pour activer toutes les fonctionnalités B2C et payer les frais d’utilisation. Pour plus d’informations, consultez [cet article](active-directory-b2c-how-to-enable-billing.md). Si vous ne liez pas votre locataire Azure AD B2C à votre abonnement Azure, certaines fonctionnalités sont bloquées et vous voyez un message d’avertissement (« Aucun abonnement lié à ce client B2C ou à l’abonnement ne requiert votre attention. ») dans les paramètres B2C. Il est important d’effectuer cette étape avant de passer vos applications en production.


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Vous pouvez également accéder au panneau en entrant `Azure AD B2C` dans **Rechercher des ressources** en haut du portail. Dans la liste des résultats, sélectionnez **Azure AD B2C** pour accéder au panneau des paramètres B2C.

## <a name="register-your-application"></a>Inscrivez votre application

Pour générer une application acceptant l’inscription et la connexion des consommateurs, vous devez commencer par inscrire cette application auprès d’un client Azure Active Directory B2C. Obtenez votre propre client en suivant la procédure décrite dans [Création d’un client Azure AD B2C](active-directory-b2c-get-started.md).

Les applications créées dans le portail Azure doivent être gérées à partir du même emplacement. Si vous modifiez des applications Azure AD B2C à l’aide de PowerShell ou d’un autre portail, elles ne sont plus prises en charge et ne fonctionnent pas avec Azure AD B2C. Consultez les détails dans la section [Applications ayant généré une erreur](#faulted-apps). 

Cet article utilise des exemples qui vous aideront à démarrer avec nos exemples. Les articles suivants fournissent plus d’informations sur ces exemples.

### <a name="navigate-to-b2c-settings"></a>Accéder aux paramètres B2C

Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général du client B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>Choisissez les étapes suivantes en fonction de votre type d’application

* [Inscrire une application web](#register-a-web-app)
* [Inscrire une API web](#register-a-web-api)
* [Inscrire une application mobile ou native](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Inscrire une application web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Créer une clé secrète client d’application web

Si votre application web appelle une API web sécurisée par Azure AD B2C, procédez comme suit :
   1. Créez une clé secrète d’application en accédant au panneau **Clés** et en cliquant sur le bouton **Générer la clé**. Prenez note de la valeur **Clé d’application** . Vous utilisez la valeur en tant que secret d’application dans le code de votre application.
   2. Cliquez sur **Accès d’API**, cliquez sur **Ajouter**, puis sélectionnez votre API web et les étendues (autorisations).

> [!NOTE]
> Une **Clé secrète d’application** est une information d’identification de sécurité importante, qui doit être correctement sécurisée.
> 

[Passer aux **étapes suivantes**](#next-steps)

### <a name="register-a-web-api"></a>Inscrire une API web

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Cliquez sur **Étendues publiées** pour ajouter des étendues si nécessaire. Par défaut, l’étendue « user_impersonation » est définie. L’étendue user_impersonation permet à d’autres applications d’accéder à cette API pour le compte de l’utilisateur connecté. Si vous le souhaitez, l’étendue user_impersonation peut être supprimée.

[Passer aux **étapes suivantes**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Inscrire une application mobile/native

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Passer aux **étapes suivantes**](#next-steps)

### <a name="choosing-a-web-app-or-api-reply-url"></a>Choix d’une URL de réponse d’API ou d’application web

Pour l’instant, les applications qui sont inscrites auprès d’Azure AD B2C sont limitées à un ensemble restreint de valeurs d’URL de réponse. L’URL de réponse pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URL de réponse doivent partager un même domaine DNS. Par exemple, vous ne pouvez pas inscrire une application web comportant l’une des URL de réponse suivantes :

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet de l’URL de réponse existante au nom DNS de l’URL de réponse que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :

* Le nom DNS complet de la nouvelle URL de réponse ne correspond pas au nom DNS de l’URL de réponse existante.
* Le nom DNS complet de la nouvelle URL de réponse n’est pas un sous-domaine de l’URL de réponse existante.

Par exemple, si l’application a cette URL de réponse :

`https://login.contoso.com`

Vous pouvez la compléter comme suit :

`https://login.contoso.com/new`

Dans ce cas, le nom DNS correspond exactement. Vous pouvez aussi définir l’URI suivant :

`https://new.login.contoso.com`

Dans ce cas, vous faites référence à un sous-domaine DNS de login.contoso.com. Si vous voulez disposer d’une application avec login-east.contoso.com et login-west.contoso.com comme URL de réponse, vous devez ajouter ces URL de réponse dans l’ordre suivant :

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Vous pouvez ajouter les deux derniers car il s’agit de sous-domaines de la première URL de réponse, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Choix d’un URI de redirection d’application native

Il existe deux points importants à prendre en considération lors du choix d’un URI de redirection pour les applications mobiles/natives :

* **Unique** : le schéma de l’URI de redirection doit être unique pour chaque application. Dans l’exemple (com.onmicrosoft.contoso.appname://redirect/path), com.onmicrosoft.contoso.appname correspond au schéma. Nous vous recommandons de suivre ce modèle. Si deux applications partagent le même schéma, l’utilisateur voit une boîte de dialogue « Choix d’une application ». Si l’utilisateur effectue un choix incorrect, la connexion échoue.
* **Complet** : l’URI de redirection doit comporter un schéma et un chemin d’accès. Le chemin d’accès doit contenir au moins une barre oblique après le domaine (par exemple, //contoso/ fonctionne, tandis que //contoso échoue).

Vérifiez que l’URI de redirection ne comporte aucun caractère spécial, tel que des traits de soulignement.

### <a name="faulted-apps"></a>Applications ayant généré une erreur

Les applications B2C ne doivent PAS être modifiées :

* Sur les autres portails de gestion des applications tels que le [Portail d’inscription des applications](https://apps.dev.microsoft.com/)
* À l’aide de l’API Graph ou de PowerShell

Si vous modifiez l’application Azure AD B2C comme décrit et tentez de la modifier de nouveau dans les fonctionnalités d’Azure AD B2C sur le portail Azure, l’application génère une erreur et n’est plus utilisable avec Azure AD B2C. Vous devez supprimer l’application, puis la recréer.

Pour supprimer l’application, accédez au [Portail d’inscription des applications](https://apps.dev.microsoft.com/), puis supprimez l’application à cet emplacement. Pour que l’application soit visible, vous devez en être le propriétaire (et non simplement un administrateur du locataire).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un client Azure AD B2C
> * Lier votre locataire à votre abonnement
> * Inscrivez votre application

> [!div class="nextstepaction"]
> [Activer une application web pour s’authentifier avec des comptes](active-directory-b2c-tutorials-web-app.md)