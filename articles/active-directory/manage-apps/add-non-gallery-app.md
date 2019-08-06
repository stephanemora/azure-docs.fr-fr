---
title: Ajouter une application hors galerie - Plateforme d’identité Microsoft | Microsoft Docs
description: Ajoutez une application hors galerie à votre locataire Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477260"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Ajouter une application non répertoriée (hors galerie) à votre organisation Azure AD

En plus des choix offerts dans la [galerie d’applications Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), vous avez la possibilité d'ajouter une **application ne figurant pas dans la galerie**. Vous pouvez ajouter n’importe quelle application déjà présente dans votre organisation, ou n’importe quelle application tierce d’un fournisseur qui ne fait pas déjà partie de la galerie Azure AD. Selon votre [contrat de licence](https://azure.microsoft.com/pricing/details/active-directory/), les fonctionnalités suivantes sont disponibles :

- Intégration libre-service de toute application prenant en charge les fournisseurs d’identité [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) (initiée par le fournisseur de services ou par le fournisseur d’identité fédérée)
- Intégration libre-service de toute application Web dont la page de connexion est basée sur le HTML et utilise une [authentification unique par mot de passe](what-is-single-sign-on.md#password-based-sso)
- Connexion libre-service des applications qui utilisent le protocole [System for Cross-Domain Identity Management (SCIM) pour l’approvisionnement d’utilisateurs](use-scim-to-provision-users-and-groups.md)
- Possibilité d'ajouter des liens à n'importe quelle application dans le [Lanceur d'application Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d'accès Azure AD](what-is-single-sign-on.md#linked-sign-on)

Cet article explique comment ajouter une application ne figurant pas dans la galerie aux **applications d'entreprise** dans le portail Azure, sans écrire de code. Si vous recherchez plutôt des instructions destinées aux développeurs sur l’intégration d’applications personnalisées avec Azure AD, consultez [Scénarios d’authentification pour Azure AD](../develop/authentication-scenarios.md). Lorsque vous développez une application utilisant un protocole moderne comme [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) pour authentifier les utilisateurs, vous pouvez l’inscrire auprès de la plateforme d'identité Microsoft à l'aide de l'expérience [Inscriptions d'applications](../develop/quickstart-register-app.md) du portail Azure.

## <a name="add-a-non-gallery-application"></a>Ajouter une application ne figurant pas dans la galerie

1. Connectez-vous au [Portail Azure Active Directory](https://aad.portal.azure.com/) à l’aide de votre compte d’administrateur de plateforme d’identité Microsoft.
1. Sélectionnez **Applications d’entreprise** > **Nouvelle application**.
2. (Facultatif mais recommandé) Dans la zone de recherche **Ajouter à partir de la galerie**, entrez le nom complet de l’application. Si l’application apparaît dans les résultats de recherche, sélectionnez-la et ignorez le reste de cette procédure.
3. Sélectionnez **Application ne figurant pas dans la galerie**. La page **Ajouter votre propre application** s’affiche.

   ![Ajouter l’application](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Entrez le nom complet de votre nouvelle application.
6. Sélectionnez **Ajouter**. La page **Vue d’ensemble** de l’application s’ouvre.

## <a name="configure-user-sign-in-properties"></a>Configurer les propriétés de connexion de l’utilisateur

1. Sélectionnez **Propriétés** pour ouvrir le volet Propriétés pour la modification.

    ![Volet Modifier des propriétés](media/add-application-portal/edit-properties.png)

1. Définissez les options suivantes pour déterminer comment les utilisateurs qui sont affectés ou non affectés à l’application peuvent s’y connecter, et si un utilisateur peut voir l’application dans le volet d’accès.

    - **Connexion permise pour les utilisateurs** détermine si les utilisateurs assignés à l’application peuvent se connecter.
    - **Affectation de l’utilisateur requise** détermine si les utilisateurs qui ne sont pas assignés à l’application peuvent se connecter.
    - **Visible par l’utilisateur** détermine si les utilisateurs assignés à une application peuvent la voir dans le volet d’accès et le lanceur d’applications O365.

      Comportement pour les utilisateurs **assignés** :

       | Paramètres de propriété d'application | | | Expérience de l’utilisateur assigné | |
       |---|---|---|---|---|
       | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Est-ce que les utilisateurs assignés peuvent se connecter ? | Est-ce que les utilisateurs assignés peuvent voir l’application ?* |
       | Oui | Oui | Oui | Oui | Oui  |
       | Oui | Oui | no  | Oui | no   |
       | Oui | no  | Oui | Oui | Oui  |
       | Oui | no  | no  | Oui | no   |
       | no  | Oui | Oui | no  | no   |
       | no  | Oui | no  | no  | no   |
       | no  | no  | Oui | no  | no   |
       | no  | no  | no  | no  | no   |

      Comportement pour les utilisateurs **non assignés** :

       | Paramètres de propriété d'application | | | Expérience de l’utilisateur non assigné | |
       |---|---|---|---|---|
       | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Est-ce que les utilisateurs non assignés peuvent se connecter ? | Est-ce que les utilisateurs non assignés peuvent voir l’application ?* |
       | Oui | Oui | Oui | no  | no   |
       | Oui | Oui | no  | no  | no   |
       | Oui | no  | Oui | Oui | no   |
       | Oui | no  | no  | Oui | no   |
       | no  | Oui | Oui | no  | no   |
       | no  | Oui | no  | no  | no   |
       | no  | no  | Oui | no  | no   |
       | no  | no  | no  | no  | no   |

     \* Est-ce que l’utilisateur peut voir l’application dans le volet d’accès et le lanceur d’applications Office 365 ?

1. Pour utiliser un logo personnalisé, créez un logo de 215 x 215 pixels et enregistrez-le au format PNG. Accédez ensuite à votre logo et chargez-le.

    ![Modifier le logo](media/add-application-portal/change-logo.png)

1. Quand vous avez terminé, Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez ajouté l’application à votre organisation Azure AD, [choisissez une méthode d’authentification unique](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que vous souhaitez utiliser et reportez-vous à l’article approprié ci-dessous :

- [Configurer l’authentification unique SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurer l’authentification unique par mot de passe](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurer l’authentification liée](configure-linked-sign-on.md)
