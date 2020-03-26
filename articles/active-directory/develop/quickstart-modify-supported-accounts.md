---
title: Modifier les comptes d’applications de plateforme des identités Microsoft | Azure
description: Configurez une application inscrite avec la plateforme d’identité Microsoft pour modifier les utilisateurs ou les comptes qui peuvent accéder à l’application.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 94fed6f4aa62c7e649cf7d644e571b30561e0da4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050240"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Démarrage rapide : Modifier les comptes pris en charge par une application

Lorsque vous inscrivez une application dans la plateforme d’identité Microsoft, vous pouvez choisir de rendre votre application accessible uniquement aux utilisateurs de votre organisation. Vous pouvez également souhaiter que votre application soit accessible aux utilisateurs des organisations externes, ou aux utilisateurs des organisations externes ainsi qu’aux utilisateurs qui ne font pas nécessairement partie d’une organisation (comptes personnels).

Dans ce démarrage rapide, vous allez apprendre à modifier la configuration de votre application afin de modifier les utilisateurs ou les comptes pouvant accéder à l’application.

## <a name="prerequisites"></a>Prérequis

Pour commencer, assurez-vous de remplir ces conditions préalables :

* Découvrez les [autorisations et consentement](v2-permissions-and-consent.md) pris en charge qu’il est important de comprendre lors de la création d’applications devant être utilisées par d’autres utilisateurs ou applications.
* Disposez d’un locataire auprès duquel des applications sont inscrites.
  * Si vous n’avez pas d’applications inscrites, [découvrez comment inscrire des applications à l’aide de la plateforme d’identité Microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Connectez-vous au portail Azure puis sélectionnez l’application

Avant de pouvoir configurer l’application, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation de gauche, sélectionnez le service **Azure Active Directory**, puis **Inscriptions d’applications**.
1. Recherchez et sélectionnez l’application que vous souhaitez configurer. Une fois l’application sélectionnée, vous pourrez voir sa **présentation** ou sa page d’inscription principale.
1. Suivez les étapes pour [modifier l’inscription d’application pour prendre en charge des comptes différents](#change-the-application-registration-to-support-different-accounts).
1. Si vous avez une application à page unique, [activez l’octroi implicite OAuth 2.0](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Modifier l’inscription d’application pour prendre en charge des comptes différents

Si vous écrivez une application que vous souhaitez proposer à vos clients ou à des partenaires externes à votre organisation, vous devez mettre à jour la définition de l’application dans le portail Azure.

> [!IMPORTANT]
> Azure AD nécessite que l’URI ID d’application des applications multilocataires soit globalement unique. L’URI ID d’application est l’une des méthodes d’identification d'une application dans les messages de protocole. Pour une application à client unique, il suffit que l’URI ID d’application soit unique au sein de ce locataire. Pour une application mutualisée, l’URI doit être globalement unique afin qu’Azure AD puisse trouver l’application sur tous les clients. L’unicité globale est appliquée en obligeant l’URI ID d’application à avoir un nom d’hôte correspondant à un domaine vérifié du client Azure AD. Par exemple, si le nom de votre locataire est contoso.onmicrosoft.com, un URI ID d’application valide serait `https://contoso.onmicrosoft.com/myapp`. Si votre locataire a un domaine vérifié de contoso.com, un URI ID d’application valide serait également `https://contoso.com/myapp`. Si l’URI ID d’application ne suit pas ce modèle, une application ne peut pas être définie comme multi-locataire.

### <a name="to-change-who-can-access-your-application"></a>Pour modifier qui peut accéder à votre application

1. À partir de la page **Vue d’ensemble** de l’application, sélectionnez la section **Authentification** et modifiez la valeur sélectionnée sous **Types de comptes pris en charge**.
    * Sélectionnez **Comptes dans ce répertoire uniquement** si vous générez une application line of business (LOB). Cette option n’est pas disponible si l’application n’est pas inscrite dans un répertoire.
    * Sélectionnez **Comptes dans n’importe quel répertoire d’organisation** si vous souhaitez cibler tous les clients professionnels et dans l’éducation.
    * Sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft** pour cibler le plus grand nombre de clients.
1. Sélectionnez **Enregistrer**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Activer l’octroi implicite OAuth 2.0 pour les applications monopages

Les applications monopages (SPA) se composent généralement d’une partie frontale reposant largement sur JavaScript qui s’exécute dans le navigateur. Celle-ci appelle l’API web principale de l’application pour effectuer sa logique métier. Pour les applications à page unique hébergées dans Azure AD, l’accord implicite OAuth 2.0 permet d’authentifier l’utilisateur auprès de Azure AD et d’obtenir un jeton qui peut servir à sécuriser les appels du client JavaScript de l’application à son API web principale.

Une fois que l’utilisateur a donné son consentement, ce même protocole d’authentification peut servir à obtenir des jetons pour sécuriser les appels entre le client et d’autres ressources API web configurées pour l’application. Pour plus d’informations sur l’octroi d’autorisation implicite et pour vous aider à décider si cette méthode est adaptée à votre scénario d’application, découvrez le flux d’octroi implicite OAuth 2.0 dans Azure AD [v1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) et [v2.0](v2-oauth2-implicit-grant-flow.md).

Par défaut, l’accord implicite OAuth 2.0 est désactivé pour les applications. Vous pouvez activer l’accord implicite OAuth 2.0 pour votre application en suivant les étapes décrites ci-dessous.

### <a name="to-enable-oauth-20-implicit-grant"></a>Pour activer l’accord implicite OAuth 2.0

1. Sélectionnez la section **Authentification** sur la page de **présentation** de l’application.
1. Sous **Paramètres avancés**, localisez la section **octroi implicite**.
1. Sélectionnez **Jetons d’ID**, **Jetons d’accès**, ou les deux.
1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les autres démarrages rapides relatifs à la gestion des applications pour les applications :

* [Inscrire une application à l’aide de la plateforme d’identités Microsoft](quickstart-register-app.md)
* [Configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md)
* [Configurer une application pour exposer les API web](quickstart-configure-app-expose-web-apis.md)
* [Supprimer une application inscrite à l’aide de la plateforme d’identité Microsoft](quickstart-remove-app.md)

Pour en savoir plus sur les deux objets Azure AD représentant une application inscrite et la relation entre ces objets, consultez [Objets application et principal du service dans Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).

Pour en savoir plus sur les directives de personnalisation que vous devez suivre lors du développement d’applications avec Azure Active Directory, consultez [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md).
