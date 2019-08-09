---
title: Authentification unique SAML pour les applications locales avec le proxy d’application Azure Active Directory (préversion) | Microsoft Docs
description: Découvrez comment fournir l’authentification unique pour les applications locales qui sont publiées via le proxy d’application et sécurisées avec l’authentification SAML.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907cb598d708bfa26f53d2e43fef5456258c21b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393048"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>Authentification unique SAML pour les applications locales avec le proxy d’application (préversion)

Vous pouvez fournir l’authentification unique (SSO) aux applications locales qui sont sécurisées avec l’authentification SAML, et fournir un accès à distance à ces applications via le proxy d’application. Avec l’authentification unique SAML, Azure Active Directory (Azure AD) s’authentifie dans l’application en utilisant le compte Azure AD de l’utilisateur. Azure AD communique les informations d’authentification à l’application via un protocole de connexion. Vous pouvez également mapper les utilisateurs à des rôles d’application basés sur les règles, que vous définissez dans vos revendications SAML. Si vous activez le proxy d’application en plus de l’authentification unique SAML, vos utilisateurs bénéficieront d’un accès externe à l’application et d’une expérience d’authentification unique très fluide.

Les applications doivent pouvoir consommer des jetons SAML émis par **Azure Active Directory**. Cette configuration ne s’applique pas aux applications qui utilisent un fournisseur d’identité local. Pour ces scénarios, nous vous recommandons de consulter [Ressources pour la migration d’applications vers Azure Active Directory](migration-resources.md).

L’authentification unique SAML avec le proxy d’application fonctionne également avec la fonctionnalité de chiffrement de jetons SAML. Pour plus d’informations, consultez [Configurer le chiffrement des jetons SAML Azure AD](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publier une application locale avec le proxy d’application

Pour fournir l’authentification unique aux applications locales, vous devez avoir activé le proxy d’application et avoir installé un connecteur. Pour plus d’informations, consultez [Ajouter une application locale pour un accès à distance via le service proxy d’application dans Azure AD](application-proxy-add-on-premises-application.md).

Lorsque vous suivrez le tutoriel, gardez à l’esprit les points suivants :

* Publiez votre application en suivant les instructions de ce tutoriel. Sélectionnez **Azure Active Directory** comme méthode de **pré-authentification** pour votre application (étape 4 [Ajouter une application locale à Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Copiez l’**URL externe** de l’application.
* Une bonne pratique consiste à utiliser des domaines personnalisés chaque fois que cela est possible, afin d’optimiser l’expérience utilisateur. Pour plus d’informations, consultez [Utilisation des domaines personnalisés dans le proxy d’application Azure AD](application-proxy-configure-custom-domain.md).
* Ajoutez au moins un utilisateur à l’application et vérifiez que le compte de test a accès à l’application locale. À l’aide du compte de test, vérifiez que vous pouvez atteindre l’application en accédant à l’**URL externe** afin de vérifier que le proxy d’application est correctement configuré. Pour obtenir des informations de dépannage, consultez [Résoudre les problèmes et les messages d’erreur liés aux proxy d’application](application-proxy-troubleshoot.md).

## <a name="set-up-saml-sso"></a>Configurer l’authentification unique SAML

1. Dans le portail Azure, sélectionnez **Azure Active Directory > Applications d’entreprise**, puis sélectionnez l’application dans la liste.
1. Dans la page **Vue d’ensemble** de l’application, sélectionnez **Authentification unique**.
1. Sélectionnez **SAML** comme méthode d’authentification unique.
1. Dans la page **Configurer l’authentification unique avec SAML**, modifiez les données situées sous **Configuration SAML de base**, puis suivez les étapes de la section [Entrer les données de la configuration SAML de base](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) afin de configurer l’authentification SAML pour l’application.

   * Vérifiez que l’**URL de réponse** correspond à l’**URL externe** de l’application locale que vous avez publiée via le proxy d’application, ou qu’il s’agit d’un chemin sous l’**URL externe**.
   * Pour un flux lancé par le fournisseur d’identité dans lequel votre application nécessite une autre **URL de réponse** pour la configuration SAML, ajoutez cette URL **supplémentaire** dans la liste, puis cochez la case en regard de celle-ci pour la désigner comme l’**URL de réponse** principale.
   * Pour un flux lancé par le fournisseur de services, vérifiez que l’application principale spécifie la bonne **URL de réponse** ou l’URL Assertion Consumer Service à utiliser pour recevoir le jeton d’authentification.

     ![Entrer les données de la configuration SAML de base](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > Si l’application back-end s’attend à ce que l’**URL de réponse** soit l’URL interne, vous devrez utiliser des [domaines personnalisés](application-proxy-configure-custom-domain.md) pour que l’URL interne et l’URL externe soient identiques, ou vous devrez installer l’extension de connexion Mes applications sur les appareils des utilisateurs. Cette extension sera automatiquement redirigée vers le service de proxy d’application qui convient. Pour installer l’extension, consultez [Extension de connexion sécurisée Mes applications](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Test de l'application

Une fois toutes ces étapes effectuées, votre application doit être opérationnelle. Pour tester l’application :

1. Ouvrez un navigateur et accédez à l’URL externe que vous avez créée lors de la publication de l’application. 
1. Connectez-vous au compte de test que vous avez attribué à l’application. Vous devriez pouvoir charger l’application et vous connecter à l’aide de l’authentification unique.

## <a name="next-steps"></a>Étapes suivantes

- [Comment le proxy d’application Azure AD fournit-il une authentification unique ?](application-proxy-single-sign-on.md)
- [Résoudre les problèmes du proxy d’application](application-proxy-troubleshoot.md)
