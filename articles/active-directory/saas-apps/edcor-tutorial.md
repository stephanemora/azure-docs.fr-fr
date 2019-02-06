---
title: 'Tutoriel : Intégration d’Azure Active Directory à Edcor | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Edcor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5b06f2d8-9cd7-498d-bdd6-88570a0a0a15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 0b3ca7a101a0c3979058b7094f8e51ed80e02649
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174514"
---
# <a name="tutorial-azure-active-directory-integration-with-edcor"></a>Tutoriel : Intégration d’Azure Active Directory à Edcor

Dans ce didacticiel, vous allez apprendre à intégrer Edcor à Azure Active Directory (Azure AD).

L’intégration d’Edcor à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Edcor.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Edcor (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Edcor, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Edcor pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Edcor à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-edcor-from-the-gallery"></a>Ajout d’Edcor à partir de la galerie
Pour configurer l’intégration d’Edcor à Azure AD, vous devez ajouter Edcor, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Edcor à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Edcor**, sélectionnez **Edcor** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Edcor dans la liste des résultats](./media/edcor-tutorial/tutorial_edcor_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Edcor, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Edcor équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Edcor associé doit être établie.

Dans Edcor, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l'authentification unique Azure AD avec Edcor, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Edcor](#create-an-edcor-test-user)** pour avoir un équivalent de Britta Simon dans Edcor, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Edcor.

**Pour configurer l’authentification unique Azure AD avec Edcor, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Edcor**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/edcor-tutorial/tutorial_edcor_samlbase.png)

3. Dans la section **Domaine et URL Edcor**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Edcor](./media/edcor-tutorial/tutorial_edcor_url1.png)

    Dans la zone de texte **Identificateur**, tapez une URL : `https://sso.edcor.com/sp/ACS.saml2`


4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/edcor-tutorial/tutorial_edcor_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/edcor-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration d’Edcor**, cliquez sur **Configurer Edcor** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration d’Edcor](./media/edcor-tutorial/tutorial_edcor_configure.png) 

7. Pour configurer l’authentification unique côté **Edcor**, vous devez envoyer le **XML de métadonnées** téléchargé, l’**URL de déconnexion** et l’**ID d’entité SAML** à l’[équipe de support technique Edcor](http://www.edcor.com/contact-us-2/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/edcor-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/edcor-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/edcor-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/edcor-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-edcor-test-user"></a>Créer un utilisateur de test Edcor

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Edcor. Collaborez avec l’ [équipe du support technique d’Edcor](http://www.edcor.com/contact-us-2/)  pour ajouter des utilisateurs à la plateforme Edcor. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.  

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Edcor.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Edcor, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Edcor**.

    ![Lien Edcor dans la liste des applications](./media/edcor-tutorial/tutorial_edcor_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Edcor dans le volet d’accès, vous devriez être automatiquement connecté à votre application Edcor.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/edcor-tutorial/tutorial_general_01.png
[2]: ./media/edcor-tutorial/tutorial_general_02.png
[3]: ./media/edcor-tutorial/tutorial_general_03.png
[4]: ./media/edcor-tutorial/tutorial_general_04.png

[100]: ./media/edcor-tutorial/tutorial_general_100.png

[200]: ./media/edcor-tutorial/tutorial_general_200.png
[201]: ./media/edcor-tutorial/tutorial_general_201.png
[202]: ./media/edcor-tutorial/tutorial_general_202.png
[203]: ./media/edcor-tutorial/tutorial_general_203.png

