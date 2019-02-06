---
title: 'Tutoriel : Intégration d’Azure Active Directory à Adobe Captivate Prime | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Captivate Prime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: aa20e4544fcd78330c0daa15b9aa058ba80af2d5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171947"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Tutoriel : Intégration d’Azure Active Directory à Adobe Captivate Prime

Dans ce tutoriel, vous allez découvrir comment intégrer Adobe Captivate Prime à Azure Active Directory (Azure AD).

L’intégration d’Adobe Captivate Prime à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adobe Captivate Prime
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adobe Captivate Prime (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Adobe Captivate Prime, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Adobe Captivate Prime pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Adobe Captivate Prime à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Ajout d’Adobe Captivate Prime à partir de la galerie
Pour configurer l’intégration d’Adobe Captivate Prime à Azure AD, vous devez ajouter Adobe Captivate Prime de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Adobe Captivate Prime à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Adobe Captivate Prime**, sélectionnez **Adobe Captivate Prime** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Adobe Captivate Prime dans la liste des résultats](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Adobe Captivate Prime, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Adobe Captivate Prime équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Adobe Captivate Prime associé doit être établie.

Pour configurer et tester l’authentification unique avec Azure AD avec Adobe Captivate Prime, vous devez effectuer les opérations suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)** pour avoir un équivalent de Britta Simon dans Adobe Captivate Prime lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Adobe Captivate Prime.

**Pour configurer l’authentification unique Azure AD avec Adobe Captivate Prime, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Adobe Captivate Prime**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. Dans la section **Domaine et URL Adobe Captivate Prime**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Adobe Captivate Prime](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL : `https://captivateprime.adobe.com`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : `https://captivateprime.adobe.com/saml/SSO`

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Accédez à l’onglet **Propriétés**, copiez l’**URL de l’accès utilisateur** et collez-la dans le Bloc-notes.

    ![Lien d’accès utilisateur](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Pour configurer l’authentification unique du côté **Adobe Captivate Prime**, vous devez envoyer le **XML des métadonnées** téléchargé et l’**URL d’accès utilisateur** copié à l’[équipe de support d’Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/adobecaptivateprime-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Créer un utilisateur de test Adobe Captivate Prime

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Adobe Captivate Prime. Collaborez avec l’ [équipe du support technique d’Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) pour ajouter des utilisateurs à la plateforme Adobe Captivate Prime. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Captivate Prime.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Adobe Captivate Prime, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Adobe Captivate Prime**.

    ![Lien Adobe Captivate Prime dans la liste des applications](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Adobe Captivate Prime dans le volet d’accès, vous devez être connecté automatiquement à votre application Adobe Captivate Prime.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/adobecaptivateprime-tutorial/tutorial_general_203.png

