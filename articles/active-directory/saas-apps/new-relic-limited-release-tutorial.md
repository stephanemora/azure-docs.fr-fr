---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à New Relic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327064"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à New Relic

Dans ce tutoriel, vous allez découvrir comment intégrer New Relic à Azure Active Directory (Azure AD). Quand vous intégrez New Relic à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à New Relic.
* Permettre à vos utilisateurs de se connecter automatiquement à New Relic avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement New Relic pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* New Relic prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identité**.
* Une fois que vous avez configuré New Relic, vous pouvez appliquer des contrôles de session, qui protègent contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>Ajouter l’application New Relic à partir de la galerie

Pour configurer l’intégration de New Relic à Azure AD, vous devez ajouter **New Relic (By Organization)** à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Sélectionnez le service **Azure Active Directory**.
1. Sélectionnez **Applications d’entreprise**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la page **Parcourir la galerie Azure AD**, tapez **New Relic (By Organization)** dans la zone de recherche.
1. Sélectionnez **New Relic (By Organization)** dans le volet de résultats, puis sélectionnez **Créer**. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Configurer et tester l’authentification unique Azure AD pour New Relic

Configurez et testez l’authentification unique Azure AD avec New Relic pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans New Relic.

Pour configurer et tester l’authentification unique Azure AD avec New Relic, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
   1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
   1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique New Relic](#configure-new-relic-sso)** pour configurer les paramètres d’authentification unique côté New Relic.
   1. **[Créer un utilisateur de test New Relic](#create-a-new-relic-test-user)** pour obtenir un équivalent de Britta Simon dans New Relic lié à l’utilisateur Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **New Relic by Organization**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.

1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.

1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez des valeurs pour **Identificateur** et **URL de réponse**.

   * Ces valeurs peuvent être récupérées à l’aide de l’application New Relic **My Organization**. Pour utiliser cette application, effectuez ces étapes :
      1. [Connectez-vous](https://login.newrelic.com/) à New Relic.
      1. Dans le menu du haut, sélectionnez **Apps** (Applications).
      1. Dans la section **Your apps** (Vos applications), sélectionnez **My Organization** (Mon organisation).
      1. Cliquez sur **Authentication domains** (Domaines d’authentification).
      1. Choisissez le domaine d’authentification auquel l’authentification unique Azure AD soit se connecter (si vous avez plusieurs domaines d’authentification). La plupart des entreprises n’ont qu’un seul domaine d’authentification appelé **Default** (Par défaut). Avec un seul domaine d’authentification, nul besoin d’effectuer une sélection.
      1. Dans la section **Authentication** (Authentification), **Assertion consumer URL** (URL du consommateur d’assertion) contient la valeur à utiliser pour **Reply URL** (URL de réponse).
      1. Dans la section **Authentication** (Authentification), **Our entity ID** (Notre ID d’entité) contient la valeur à utiliser pour **Identifier** (Identificateur).

1. Dans la section **User Attributes & Claims** (Attributs et revendications utilisateur), vérifiez que **Unique User Identifier** (Identificateur utilisateur unique) est mappé à un champ contenant l’adresse e-mail utilisée dans New Relic.

   * Le champ par défaut **user.userprincipalname** fonctionne pour vous si ses valeurs correspondent aux adresses e-mail New Relic.
   * Il se peut que le champ **user.mail** fonctionne mieux si **user.userprincipalname** n’est pas l’adresse e-mail New Relic.

1. Dans la section **Certificat de signature SAML**, copiez la valeur de **URL des métadonnées de fédération d’application**, puis enregistrez-la pour une utilisation ultérieure.

1. Dans la section **Configurer New Relic by Organization**, copiez la valeur de **URL de connexion**, puis enregistrez-la pour une utilisation ultérieure.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le portail Azure, sélectionnez le service **Azure Active Directory**.
1. Sélectionnez **Utilisateurs**.
1. Pour ajouter un nouvel utilisateur, sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans la page **Nouvel utilisateur**, suivez ces étapes :
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `b.simon@contoso.com`. Cela doit correspondre à l’adresse e-mail que vous utiliserez côté New Relic.
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Cochez la case **Afficher le mot de passe**, puis enregistrez la valeur affichée dans le champ **Mot de passe initial**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à l’application **New Relic (By Organization)** .

1. Dans le portail Azure, sélectionnez le service **Azure Active Directory**.
1. Sélectionnez **Applications d’entreprise**.
1. Dans la liste des applications, sélectionnez **New Relic by Organization**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** (ou **Utilisateurs**, selon le niveau de votre plan) dans la boîte de dialogue **Ajouter une attribution**.

   ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes** (ou **Utilisateurs**), sélectionnez **B.Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-new-relic-sso"></a>Configurer l’authentification unique pour New Relic

Pour configurer l’authentification unique dans New Relic, suivez ces étapes.

1. [Connectez-vous](https://login.newrelic.com/) à New Relic.

1. Dans le menu du haut, sélectionnez **Apps** (Applications).

1. Dans la section **Your apps** (Vos applications), sélectionnez **My Organization** (Mon organisation).

1. Cliquez sur **Authentication domains** (Domaines d’authentification).

1. Choisissez le domaine d’authentification auquel l’authentification unique Azure AD soit se connecter (si vous avez plusieurs domaines d’authentification). La plupart des entreprises n’ont qu’un seul domaine d’authentification appelé **Default** (Par défaut). Avec un seul domaine d’authentification, nul besoin d’effectuer une sélection.

1. Dans la section **Authentication** (Authentification), cliquez sur **Configure** (Configurer).

   1. Dans le champ **Source of SAML metadata** (Source des métadonnées SAML), entrez la valeur que vous avez enregistrée précédemment à partir du champ **URL des métadonnées de fédération d’application** côté Azure AD.

   1. Dans le champ **SSO target URL** (URL cible SSO), entrez la valeur que vous avez enregistrée précédemment à partir du champ **URL de connexion** côté Azure AD.

   1. Cliquez sur **Save** (Enregistrer) après avoir vérifié que les paramètres sont bien configurés du côté d’Azure AD et de New Relic. Si la configuration est incorrecte des deux côtés, vos utilisateurs ne pourront pas se connecter à New Relic.

### <a name="create-a-new-relic-test-user"></a>Créer un utilisateur de test New Relic

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans New Relic. Pour créer l’utilisateur, suivez ces étapes.

1. [Connectez-vous](https://login.newrelic.com/) à New Relic.

1. Dans le menu du haut, sélectionnez **Apps** (Applications).

1. Dans la section **Your apps** (Vos applications), sélectionnez **User Management** (Gestion des utilisateurs).

1. Cliquez sur le bouton **Add user** (Ajouter un utilisateur).

   1. Dans le champ **Nom**, entrez **B.Simon**.
   
   1. Dans le champ **Email** (E-mail), entrez la valeur qui sera envoyée par l’authentification unique Azure AD.
   
   1. Choisissez un type d’utilisateur dans **Type** et un groupe d’utilisateurs dans **Group** pour l’utilisateur. Pour un utilisateur de test, **Basic User** (Utilisateur de base) pour Type et **User** (Utilisateur) pour Group sont des choix raisonnables.
   
   1. Cliquez sur **Add User** (Ajouter un utilisateur) pour enregistrer l’utilisateur.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette **New Relic by Organization** dans le panneau d’accès doit vous connecter automatiquement à l’application New Relic. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](./tutorial-list.md)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)

- [Essayer New Relic avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](/cloud-app-security/proxy-intro-aad)
