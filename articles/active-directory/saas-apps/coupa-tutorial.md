---
title: 'Tutoriel : Intégration d’Azure Active Directory à Coupa | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: 50d2e6a7ae1f34232a20e244ac65c2d3678f4f97
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153928"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Didacticiel : Intégration d’Azure Active Directory à Coupa

Dans ce didacticiel, vous allez apprendre à intégrer Coupa à Azure Active Directory (Azure AD).

L’intégration de Coupa dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Coupa.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Coupa (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l'intégration d'Azure AD avec Coupa, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Coupa pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Coupa à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-coupa-from-the-gallery"></a>Ajout de Coupa à partir de la galerie
Pour configurer l'intégration de Coupa avec Azure AD, vous devez ajouter Coupa disponible dans la galerie à votre liste d'applications SaaS gérées.

**Pour ajouter Coupa à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Coupa**, sélectionnez **Coupa** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Coupa dans la liste des résultats](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Coupa avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Coupa équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Coupa associé doit être établie.

Dans Coupa, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l'authentification unique Azure AD avec Coupa, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Coupa](#create-a-coupa-test-user)** pour avoir un équivalent de Britta Simon dans Coupa lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Coupa.

**Pour configurer l’authentification unique Azure AD avec Coupa, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Coupa**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

1. Dans la section **Domaine et URL Coupa**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Coupa](./media/coupa-tutorial/tutorial_coupa_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.coupahost.com`

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez l’[équipe de support technique de Coupa](https://success.coupa.com/Support/Contact_Us?).

    b. Dans la zone de texte **Identificateur**, tapez l’URL suivante :

    | Environnement  | URL |
    |:-------------|----|
    | Bac à sable | `devsso35.coupahost.com`|
    | Production | `prdsso40.coupahost.com`|
    | | |

    c. Dans la zone de texte **URL de réponse**, tapez l’URL suivante :

    | Environnement | URL |
    |------------- |----|
    | Bac à sable | `https://devsso35.coupahost.com/sp/ACS.saml2`|
    | Production | `https://prdsso40.coupahost.com/sp/ACS.saml2`|
    | | |

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/coupa-tutorial/tutorial_general_400.png)

1. Connectez-vous à votre site d’entreprise Coupa en tant qu’administrateur.

1. Accédez à **Setup \> Security controls**.

   ![Security Controls](./media/coupa-tutorial/ic791900.png "Security Controls")

1. Dans la section **Log in using Coupa credentials** , procédez comme suit :

    ![Coupa SP metadata](./media/coupa-tutorial/ic791901.png "Coupa SP metadata")

    a. Sélectionnez **Log in using SAML**.

    b. Cliquez sur **Parcourir** pour charger les métadonnées téléchargées à partir du portail Azure.

    c. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/coupa-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/coupa-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/coupa-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/coupa-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-coupa-test-user"></a>Créer un utilisateur de test Coupa

Pour se connecter à Coupa, les utilisateurs d’Azure AD doivent être approvisionnés dans Coupa.  

* Dans le cas de Coupa, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Coupa** en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Setup**, puis sur **Users**.

   ![Utilisateurs](./media/coupa-tutorial/ic791908.png "Utilisateurs")

1. Cliquez sur **Créer**.

   ![Create Users](./media/coupa-tutorial/ic791909.png "Create Users")

1. Dans la section **User Create** , procédez comme suit :

   ![Détails de l’utilisateur](./media/coupa-tutorial/ic791910.png "Détails de l’utilisateur")

   a. Tapez l’ID de connexion, le prénom, le nom, l’ID d’authentification unique et l’adresse électronique d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte **Login**, **First name**, **Last Name**, **Single Sign-On ID** et **Email**.

   b. Cliquez sur **Créer**.

   >[!NOTE]
   >Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien pour confirmer le compte et l’activer.
   >

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par Coupa pour approvisionner des comptes d’utilisateurs AAD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Coupa.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Coupa, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

1. Dans la liste des applications, sélectionnez **Coupa**.

    ![Lien Coupa dans la liste des applications](./media/coupa-tutorial/tutorial_coupa_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Coupa dans le volet d’accès, vous devez être connecté automatiquement à votre application Coupa.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/coupa-tutorial/tutorial_general_01.png
[2]: ./media/coupa-tutorial/tutorial_general_02.png
[3]: ./media/coupa-tutorial/tutorial_general_03.png
[4]: ./media/coupa-tutorial/tutorial_general_04.png

[100]: ./media/coupa-tutorial/tutorial_general_100.png

[200]: ./media/coupa-tutorial/tutorial_general_200.png
[201]: ./media/coupa-tutorial/tutorial_general_201.png
[202]: ./media/coupa-tutorial/tutorial_general_202.png
[203]: ./media/coupa-tutorial/tutorial_general_203.png
