---
title: 'Tutoriel : Intégration d’Azure Active Directory à SanSan | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SanSan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.openlocfilehash: 65c3e3df9fe62614eff15585373360ebcaa158cf
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543327"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>Tutoriel : Intégrer Sansan à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer SanSan à Azure Active Directory (Azure AD). Quand vous intégrez Sansan à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Sansan.
* Permettre à vos utilisateurs de se connecter automatiquement à Sansan avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement SanSan pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. Sansan prend en charge l’authentification unique lancée par le **fournisseur de services**.

## <a name="adding-sansan-from-the-gallery"></a>Ajout de SanSan à partir de la galerie

Pour configurer l’intégration de SanSan à Azure AD, vous devez ajouter SanSan à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Sansan** dans la zone de recherche.
1. Sélectionnez **Sansan** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Sansan à l’aide d’un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Sansan associé.

Pour configurer et tester l’authentification unique Azure AD avec Sansan, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer Sansan](#configure-sansan)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Sansan](#create-sansan-test-user)** pour avoir dans Sansan un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Sansan**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la boîte de dialogue **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    1. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    
    | Environnement | URL |
    |:--- |:--- |
    | Web PC |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Application mobile native |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Paramètres de navigateur mobile |`https://ap.sansan.com/s/saml2/<company name>/acs` |

    2. Dans la zone de texte **Identificateur (ID d’entité)** , vous pouvez configurer plusieurs valeurs d’identificateur et sélectionner l’un d’eux selon les environnements.

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez l’[équipe du support technique Sansan](https://www.sansan.com/form/contact). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Sansan**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-sansan"></a>Configurer Sansan

Pour configurer l’authentification unique côté **Sansan**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL copiées appropriées du portail Azure à l’[équipe du support technique Sansan](https://www.sansan.com/form/contact). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le Portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `Britta Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SanSan.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SanSan**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-sansan-test-user"></a>Créer un utilisateur de test Sansan

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Sansan. L’utilisateur doit faire l’objet d’une attribution dans l’application Sansan pour pouvoir bénéficier de l’authentification unique.

> [!NOTE]
> Si vous avez besoin de créer un utilisateur manuellement ou un groupe d’utilisateurs, vous devez contacter l’[équipe de support technique SanSan](https://www.sansan.com/form/contact).

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette Sansan dans le volet d’accès, vous devez être connecté automatiquement à l’application Sansan pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)