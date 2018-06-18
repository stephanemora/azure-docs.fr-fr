---
title: Options avancées de signature de certificats dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser les options avancées de signature de certificats dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 16cb3e034bd928bbae8ae1f2e8d6b94b9ec699e4
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302201"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Options avancées de signature de certificats dans le jeton SAML pour les applications de galerie dans Azure Active Directory
Aujourd’hui Azure Active Directory (Azure AD) prend en charge des milliers d’applications pré-intégrées dans la galerie d’applications Azure Active Directory. Ce nombre comprend plus de 500 applications qui prennent en charge l’authentification unique à l’aide du protocole SAML 2.0. Quand un utilisateur s’authentifie auprès d’une application par l’intermédiaire d’Azure AD en utilisant SAML, Azure AD envoie un jeton à l’application (via HTTP POST). Après quoi, l’application valide et utilise ce jeton pour connecter l’utilisateur au lieu de lui demander un nom d’utilisateur et un mot de passe. Ces jetons SAML sont signés avec le certificat unique qui est généré dans Azure AD et par des algorithmes standard spécifiques.

Azure AD utilise certains des paramètres par défaut pour les applications de la galerie. Les valeurs par défaut sont configurées selon les exigences de l’application.

Azure AD prend en charge les paramètres avancés de signature de certificat. Pour sélectionner ces options, cochez d’abord la case **Afficher les paramètres avancés de signature de certificat** :

![Afficher les paramètres avancés de signature de certificat](./media/certificate-signing-options/saml-advance-certificate.png)

Une fois cette case cochée, vous pouvez configurer les options de signature de certificat et l’algorithme de signature de certificat.

## <a name="certificate-signing-options"></a>Options de signature de certificat

Azure AD prend en charge trois options de signature de certificat :

* **Signer l’assertion SAML**. Cette option par défaut est définie pour la plupart des applications de la galerie. Si cette option est sélectionnée, Azure AD en tant que IdP signe les assertions et le certificat SAML avec le certificat X509 de l’application. Il utilise également l’algorithme de signature sélectionné dans la liste déroulante **Algorithme de signature**.

* **Signer la réponse SAML**. Si cette option est sélectionnée, Azure AD en tant que IdP signe la réponse SAML avec le certificat X509 de l’application. Il utilise également l’algorithme de signature sélectionné dans la liste déroulante **Algorithme de signature**.

* **Signer la réponse et l’assertion SAML**. Si cette option est sélectionnée, Azure AD en tant que IdP signe le jeton SAML entier avec le certificat X509 de l’application. Il utilise également l’algorithme de signature sélectionné dans la liste déroulante **Algorithme de signature**.

    ![Options de signature de certificat](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>Algorithmes de signature de certificat

Azure AD prend en charge deux algorithmes de signature pour signer la réponse SAML :

* **SHA-256**. Azure AD utilise cet algorithme par défaut pour signer la réponse SAML. Il s’agit de l’algorithme le plus récent, qui est considéré comme plus sécurisé que le SHA-1. La plupart des applications prennent en charge l’algorithme SHA-256. Si une application prend en charge uniquement SHA-1 comme algorithme de signature, vous pouvez la modifier. Dans le cas contraire, nous vous recommandons d’utiliser l’algorithme SHA-256 pour la signature de la réponse SAML.

    ![Algorithme de signature de certificat SHA-256](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **SHA-1**. Il s’agit de l’ancien algorithme, qui est considéré comme moins sécurisé que SH-256. Si une application prend uniquement en charge cet algorithme de signature, vous pouvez sélectionner cette option dans la liste déroulante **Algorithme de signature**. Azure AD signe alors la réponse SAML avec l’algorithme SHA-1.

    ![Algorithme de signature de certificat SHA-1](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>Étapes suivantes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](../active-directory-apps-index.md)
* [Configurer l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Résoudre les erreurs d’authentification unique basée sur SAML](../develop/active-directory-saml-debugging.md)


