---
title: Publication de votre application Azure Active Directory B2C dans la galerie d'applications Azure Active Directory
description: Apprenez à répertorier une application Azure AD B2C qui prend en charge l’authentification unique dans la galerie d’applications Azure Active Directory.
titleSuffix: Azure AD B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4413832661ffe2eea6fd48f8337618e628f2094b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562142"
---
# <a name="publish-your-azure-ad-b2c-app-to-the-azure-ad-app-gallery"></a>Publier votre application Azure AD B2C dans la galerie d’applications Azure AD

La galerie d’applications Azure Active Directory (Azure AD) est un catalogue de milliers d’applications. La galerie d’applications facilite le déploiement et la configuration de l’authentification unique (SSO) et l’automatisation de la configuration utilisateur. Vous pouvez trouver des applications Cloud populaires dans la Galerie, telles que les Workday, ServiceNow et Zoom.

Cet article explique comment publier votre application Azure Active Directory B2C (Azure AD B2C) dans la galerie d’applications Azure AD. Quand votre application est publiée, elle est répertoriée parmi les options que les clients peuvent choisir lorsqu’ils ajoutent des applications à leur locataire Azure AD.

Voici quelques-uns des avantages de l’ajout de votre application Azure AD B2C à la galerie d’applications :  

- Votre application est une intégration vérifiée avec Microsoft.
- L’accès SSO est activé entre votre application et les applications Azure AD.
- Les clients peuvent trouver votre application dans la galerie grâce à une recherche rapide.
- La configuration de l’application est simple et minimale.
- Les clients obtiennent un didacticiel de configuration pas à pas.
- Les clients peuvent attribuer l’application à différents utilisateurs et groupes au sein de leur organisation.
- L’administrateur locataire peut octroyer le consentement de l’administrateur au niveau du locataire à votre application.

## <a name="sign-in-flow-overview"></a>Vue d’ensemble du flux de connexion

Le flux de connexion implique les étapes suivantes :

1. Les utilisateurs accèdent au [portail Mes applications](https://myapps.microsoft.com/) et sélectionnent votre application, ce qui ouvre l’URL de connexion à l’application.
1. L’URL de connexion de l’application démarre une requête d’autorisation et redirige les utilisateurs vers le point de terminaison d’autorisation Azure AD B2C.
1. Les utilisateurs choisissent de se connecter avec leur compte « entreprise » Azure AD. Azure AD B2C les dirige vers le point de terminaison d’autorisation Azure AD, où ils se connectent avec leur compte professionnel.
1. Si la session d’authentification unique Azure AD B2C est active, Azure AD émet un jeton d’accès sans inviter les utilisateurs à se reconnecter. Si la session Azure AD expire ou devient non valide, les utilisateurs sont invités à se connecter à nouveau.

![Diagramme du flux de connexion OpenID Connect.](./media/publish-app-to-azure-ad-app-gallery/app-gallery-sign-in-flow.png)

Selon la session SSO des utilisateurs et les paramètres d’identité Azure AD, ils peuvent être invités à :

- Indiquer leur adresse e-mail ou numéro de téléphone.
- Entrer leur mot de passe et à se connecter à l’[application Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).
- Terminer l’authentification multifacteur.
- Accepter la page de consentement. L’administrateur locataire de votre client peut [octroyer le consentement de l’administrateur au niveau du locataire à une application](../active-directory/manage-apps/grant-admin-consent.md). Lorsque le consentement est octroyé, la page de consentement n’est pas présentée aux utilisateurs.

Une fois la connexion établie, Azure AD retourne un jeton à Azure AD B2C. Azure AD B2C valide et lit les revendications de jeton, puis retourne un jeton à votre application.

## <a name="prerequisites"></a>Configuration requise

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="step-1-register-your-application-in-azure-ad-b2c"></a>Étape 1 : Inscrire votre application dans Azure AD B2C

Pour permettre la connexion à votre application avec Azure AD B2C, inscrivez votre application dans le répertoire Azure AD B2C. L’inscription de votre application établit une relation de confiance entre l’application et Azure AD B2C. 

Si ce n’est déjà fait, [inscrivez une application web](tutorial-register-applications.md) et [activez l’octroi implicite de jeton d’ID](tutorial-register-applications.md#enable-id-token-implicit-grant). Ensuite, vous allez inscrire cette application auprès de la galerie d’applications Azure.

## <a name="step-2-set-up-sign-in-for-multitenant-azure-ad"></a>Étape 2 : Configurer la connexion pour les multi-locataires Azure AD

Pour permettre aux employés et aux consommateurs de n’importe quel locataire Azure AD de se connecter à l’aide d’Azure AD B2C, suivez les instructions pour [configurer la connexion pour les multilocataires Azure AD](identity-provider-azure-ad-multi-tenant.md?pivots=b2c-custom-policy).

## <a name="step-3-prepare-your-app"></a>Étape 3 – Préparer votre application

Dans votre application, copiez l’URL du point de terminaison de connexion. Si vous utilisez l’[exemple d’application web](configure-authentication-sample-web-app.md), l’URL de connexion est `https://localhost:5001/MicrosoftIdentity/Account/SignIn?`. Cette URL est l’emplacement où la galerie d’applications Azure AD permet aux utilisateurs de se connecter à votre application.

Dans un environnement de production, l’URI de redirection de l’inscription de l’application est généralement un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://woodgrovedemo.com/Account/SignIn`. L’URL de réponse doit commencer par `https`.

## <a name="step-4-publish-your-azure-ad-b2c-app"></a>Étape 4 : Publier votre application Azure AD B2C

Enfin, ajoutez l’application multilocataire à la galerie d’applications Azure AD. Suivez les instructions dans [Publier votre application dans la galerie d’applications Azure AD](../active-directory/develop/v2-howto-app-gallery-listing.md). Pour ajouter votre application à la galerie d’applications, procédez comme suit :

1. [Créez et publiez la documentation](../active-directory/develop/v2-howto-app-gallery-listing.md#step-5---create-and-publish-documentation).
1. [Envoyez votre application](../active-directory/develop/v2-howto-app-gallery-listing.md#step-6---submit-your-app) avec les informations suivantes :

    |Question  |Réponse à fournir  |
    |---------|---------|
    |Quel type de requête voulez-vous envoyer ?| Sélectionnez **Répertorier mon application dans la galerie**.|
    |Quelle fonctionnalité souhaitez-vous activer lorsque votre application est répertoriée dans la galerie ? | Sélectionnez **SSO fédérée(SAML, WS-Fed & OpenID Connect)** . | 
    | Sélectionner votre protocole de fédération d’application| Sélectionnez **OpenID Connect & OAuth 2.0**. |
    | ID d’application (client) | Fournissez l’ID de [votre application Azure AD B2C](#step-1-register-your-application-in-azure-ad-b2c). |
    | URL de connexion à l'application|Fournissez l’URL de connexion à l’application telle qu’elle est configurée à l'[Étape 3. Préparer votre application](#step-3-prepare-your-app).|
    | Multi-locataire| Sélectionnez **Oui**. |
    | | |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à [Publier votre application dans la galerie d’applications Azure AD](../active-directory/develop/v2-howto-app-gallery-listing.md).
