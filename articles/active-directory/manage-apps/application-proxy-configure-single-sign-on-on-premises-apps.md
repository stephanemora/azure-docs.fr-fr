---
title: SAML authentification unique pour les applications locales avec Azure Active Directory Application Proxy (version préliminaire) | Microsoft Docs
description: Découvrez comment fournir l’authentification unique pour local applications publiées via le Proxy d’Application qui sont sécurisés avec l’authentification SAML.
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
ms.openlocfilehash: 5524576ef21830ae13526dad2d8ac8a1d0864cf1
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956876"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML authentification unique pour les applications sur site avec le Proxy d’Application (version préliminaire)

Vous pouvez fournir l’authentification unique (SSO) aux applications locales qui sont sécurisées avec l’authentification SAML et fournissent un accès à distance à ces applications via le Proxy d’Application. Avec SAML authentification unique, Azure Active Directory (Azure AD) s’authentifie l’application en utilisant le compte d’utilisateur Azure AD. Azure AD communique les informations d’authentification à l’application via un protocole de connexion. Vous pouvez également mapper les utilisateurs aux rôles d’application spécifique en fonction de règles que vous définissez dans vos demandes SAML. En activant le Proxy d’Application en plus de SSO SAML vos utilisateurs ont un accès externe à l’application et une expérience de l’authentification unique transparente.

Les applications doivent être en mesure de consommer des jetons SAML émis par **Azure Active Directory**. Cette configuration ne s’applique pas aux applications à l’aide d’un fournisseur d’identité en local. Pour ces scénarios, nous vous recommandons de consulter [ressources sur la migration d’applications vers Azure AD](migration-resources.md).

SAML SSO avec le Proxy d’Application fonctionne également avec la fonctionnalité de chiffrement des jetons SAML. Pour plus d’informations, consultez [chiffrement des jetons configurer Azure AD SAML](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publier l’application sur site avec le Proxy d’Application

Avant de pouvoir fournir l’authentification unique pour les applications en local, assurez-vous que vous avez activé le Proxy d’Application et que vous avez un connecteur est installé. Consultez [ajouter une application en local pour l’accès à distance via le Proxy d’Application dans Azure AD](application-proxy-add-on-premises-application.md) pour découvrir comment.

Gardez à l’esprit les points suivants lorsque vous allez le didacticiel :

* Publier votre application en suivant les instructions dans le didacticiel. Veillez à sélectionner **Azure Active Directory** en tant que le **pré-authentification** méthode pour votre application (étape 4 [ajouter une application en local à Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Copie le **URL externe** pour l’application.
* En tant que meilleure pratique, utilisez autant que possible des domaines personnalisés pour une expérience utilisateur optimisée. En savoir plus sur [utilisation des domaines personnalisés dans le Proxy d’Application Azure AD](application-proxy-configure-custom-domain.md).
* Ajouter au moins un utilisateur à l’application et vérifiez que le compte de test a accès à l’application en local. À l’aide de l’essai de compte de test si vous pouvez d’atteindre l’application en vous rendant sur le **URL externe** pour valider le Proxy d’Application est correctement configuré. Pour des informations de dépannage, consultez [les problèmes de Proxy d’Application résoudre les problèmes et messages d’erreur](application-proxy-troubleshoot.md).

## <a name="set-up-saml-sso"></a>Configurer la SSO SAML

1. Dans le portail Azure, sélectionnez **Azure Active Directory > applications d’entreprise** et sélectionnez l’application dans la liste.
1. À partir de l’application **vue d’ensemble** page, sélectionnez **Single sign-on**.
1. Sélectionnez **SAML** en tant que la méthode d’authentification unique.
1. Dans le **définir des Single Sign-On with SAML** page, modifiez le **base SAML Configuration** données et suivez les étapes de [configuration de SAML base entrée](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) configuration basée sur SAML authentification pour l’application.

   * Assurez-vous que le **URL de réponse** correspond ou est un chemin d’accès sous le **URL externe** pour l’application en local que vous avez publiée via le Proxy d’Application. 
   * Si votre application nécessite une autre **URL de réponse** pour la configuration de SAML, ajoutez cette solution comme un **supplémentaires** URL dans la liste et la marque de la case à cocher en regard de celle-ci pour la désigner comme le serveur principal  **L’URL de réponse** d’envoyer des réponses à SAML initié du fournisseur d’identité.
   * Pour un flux initié par SP, vérifiez que l’application spécifie également la bonne **URL de réponse** ou URL Assertion Consumer Service à utiliser pour recevoir le jeton d’authentification.

     ![Entrez les données de configuration de base SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > Si l’application principale attend le **URL de réponse** pour être l’URL interne, vous devez installer l’extension mes applications sécurisée connectez-vous sur les appareils des utilisateurs. Cette extension est automatiquement redirigé vers le Service de Proxy d’Application approprié. Pour installer l’extension, consultez [sécurisée à mes applications extension de connexion](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Test de l'application

Une fois toutes ces étapes effectuées, votre application doit être opérationnelle. Pour tester l’application :

1. Ouvrez un navigateur et accédez à l’URL externe que vous avez créé quand vous avez publié l’application. 
1. Connectez-vous au compte de test que vous avez attribué à l’application. Il se peut que vous devez être en mesure de charger l’application et avoir l’authentification unique dans l’application.

## <a name="next-steps"></a>Étapes suivantes

- [Comment le proxy d’application Azure AD fournit-il une authentification unique ?](application-proxy-single-sign-on.md)
- [Résoudre les problèmes du proxy d’application](application-proxy-troubleshoot.md)
