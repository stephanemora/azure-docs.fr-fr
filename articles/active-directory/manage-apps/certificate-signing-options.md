---
title: Options avancées de signature de certificats dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser les options avancées de signature de certificats dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory
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
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1baf4af41fd5dcb6723b6ee2827ae91b43b072d9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780961"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Options avancées de signature de certificats dans le jeton SAML pour les applications de galerie dans Azure Active Directory

Aujourd’hui Azure Active Directory (Azure AD) prend en charge des milliers d’applications pré-intégrées dans la galerie d’applications Azure Active Directory. Plus de 500 applications qui prennent en charge l’authentification unique à l’aide de la [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 de protocole, tel que le [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) application. Lorsqu’un client s’authentifie à une application via Azure AD à l’aide de SAML, Azure AD envoie un jeton à l’application (via une requête HTTP POST). L’application valide et utilise le jeton pour la connexion du client au lieu de demander un nom d’utilisateur et le mot de passe. Ces jetons SAML sont signés avec le certificat unique qui est généré dans Azure AD et par des algorithmes standard spécifiques.

Azure AD utilise certains des paramètres par défaut pour les applications de la galerie. Les valeurs par défaut sont configurées selon les exigences de l’application.

Dans Azure AD, vous pouvez configurer les options de signature de certificat et l’algorithme de signature de certificat.

## <a name="certificate-signing-options"></a>Options de signature de certificat

Azure AD prend en charge trois options de signature de certificat :

* **Signer l’assertion SAML**. Cette option par défaut est définie pour la plupart des applications de la galerie. Si vous sélectionnez cette option, Azure AD comme fournisseur d’identité (IdP) signe l’assertion SAML et le certificat avec le [X.509](https://wikipedia.org/wiki/X.509) certificat de l’application.

* **Signer la réponse SAML**. Si vous sélectionnez cette option, Azure AD en tant que IdP signe la réponse SAML avec le certificat X.509 de l’application.

* **Signer la réponse et l’assertion SAML**. Si vous sélectionnez cette option, Azure AD en tant que IdP signe le jeton SAML entier avec le certificat X.509 de l’application.

## <a name="certificate-signing-algorithms"></a>Algorithmes de signature de certificat

Azure AD prend en charge deux algorithmes de signature ou des algorithmes de hachage sécurisé (SHA), pour signer la réponse SAML :

* **SHA-256**. Azure AD utilise cet algorithme par défaut pour signer la réponse SAML. Il est l’algorithme le plus récent et est plus sécurisé que SHA-1. La plupart des applications prennent en charge l’algorithme SHA-256. Si une application prend en charge uniquement SHA-1 comme algorithme de signature, vous pouvez la modifier. Dans le cas contraire, nous vous recommandons d’utiliser l’algorithme SHA-256 pour la signature de la réponse SAML.

* **SHA-1**. Cet algorithme est antérieure, et il est considéré comme moins sécurisé que SHA-256. Si une application prend uniquement en charge cet algorithme de signature, vous pouvez sélectionner cette option dans la liste déroulante **Algorithme de signature**. Azure AD signe alors la réponse SAML avec l’algorithme SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Modifier les options de signature et l’algorithme de signature de certificat

Pour modifier le certificat SAML de l’application options de signature et l’algorithme de signature de certificat, sélectionnez l’application en question :

1. Dans le [portail Azure Active Directory](https://aad.portal.azure.com/), connectez-vous à votre compte. Le **centre d’administration Azure Active Directory** page s’affiche.
1. Dans le volet gauche, sélectionnez **Applications d’entreprise**. Une liste des applications d’entreprise dans votre compte s’affiche.
1. Sélectionnez une application. Une page de présentation de l’application s’affiche.

   ![Page Vue d’ensemble de l’application](./media/certificate-signing-options/application-overview-page.png)

Ensuite, modifiez l’options dans le jeton SAML pour l’application de la signature de certificat :

1. Dans le volet gauche de la page de vue d’ensemble d’application, sélectionnez **Single sign-on**.

2. Si le **définir des Single Sign-On with SAML - version préliminaire** page s’affiche, passez à l’étape 5.

3. Si le **sélectionner une méthode d’authentification unique** page n’apparaît pas, sélectionnez **modifier les modes d’authentification uniques** pour afficher cette page.

4. Dans le **sélectionner une méthode d’authentification unique** , sélectionnez **SAML** si elle est disponible. (Si **SAML** n’est pas disponible, l’application ne prend en charge SAML, et vous pouvez ignorer le reste de cette procédure et l’article.)

5. Dans le **définir des Single Sign-On with SAML - aperçu** page, recherchez le **certificat de signature SAML** titre et sélectionnez le **modifier** icône (un crayon). Le **certificat de signature SAML** page s’affiche.

   ![Page de signature SAML](./media/certificate-signing-options/saml-signing-page.png)

6. Dans le **Option de signature** liste déroulante, choisissez **réponse de connexion SAML**, **signer l’assertion SAML**, ou **réponse de connexion SAML et l’assertion**. Descriptions de ces options s’affichent plus haut dans cet article dans le [options de signature de certificat](#certificate-signing-options).

7. Dans le **algorithme de signature** liste déroulante, choisissez **SHA-1** ou **SHA-256**. Descriptions de ces options s’affichent plus haut dans cet article dans le [algorithmes de signature de certificat](#certificate-signing-algorithms) section.

8. Si vous êtes satisfait de votre choix, sélectionnez **enregistrer** pour appliquer la nouvelle SAML les paramètres de certificat de signature. Sinon, sélectionnez le **X** à ignorer les modifications.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Résoudre les erreurs d’authentification unique basée sur SAML](../develop/howto-v1-debug-saml-sso-issues.md)
