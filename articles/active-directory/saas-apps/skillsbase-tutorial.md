---
title: 'Tutoriel : Intégration d’Azure Active Directory à Skills Base | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Skills Base.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 237d90c4-8243-4f80-a305-b5ad9204159e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2018
ms.author: jeedes
ms.openlocfilehash: e11ba8ca9c4ad17b2ade909bb474ad2d1fcf4410
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205372"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>Tutoriel : Intégration d’Azure Active Directory à Skills Base

L’objectif de ce tutoriel est de vous apprendre à intégrer Skills Base à Azure Active Directory (Azure AD).

L’intégration de Skills Base à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Skills Base.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Skills Base (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Skills Base, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à Skills Base pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Skills Base à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-skills-base-from-the-gallery"></a>Ajout de Skills Base à partir de la galerie
Pour configurer l’intégration de Skills Base à Azure AD, vous devez ajouter Skills Base à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Skills Base à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez **Skills Base**, sélectionnez **Skills Base** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Skills Base dans la liste des résultats](./media/skillsbase-tutorial/tutorial_skillsbase_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Skills Base, via un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur Skills Base équivaut à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Skills Base qui lui est associé.

Pour configurer et tester l’authentification unique d’Azure AD avec Skills Base, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Skills Base](#create-a-skills-base-test-user)** pour avoir un équivalent de Britta Simon dans Skills Base, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure, et configurer l’authentification unique dans votre application Skills Base.

**Pour configurer l’authentification unique Azure AD avec Skills Base, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Skills Base**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/skillsbase-tutorial/tutorial_skillsbase_samlbase.png)

3. Dans la section **Domaine et URL Skills Base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Skills Base](./media/skillsbase-tutorial/tutorial_skillsbase_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://app.skills-base.com/o/<customer-unique-key>`

    > [!NOTE] 
    > Vous pouvez obtenir l’URL d’authentification à partir de l’application Skills Base. Connectez-vous en tant qu’administrateur et accédez à Administration -> Paramètres -> Détails de l’instance -> lien de raccourci. Copiez l’URL d’authentification et collez-la dans la zone de texte au-dessus.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/skillsbase-tutorial/tutorial_skillsbase_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/skillsbase-tutorial/tutorial_general_400.png)

6. Ouvrez une autre fenêtre de navigateur web, puis connectez-vous à Skills Base en tant qu’administrateur de la sécurité.

7. À gauche du menu, sous **ADMIN** (Administrateur) cliquez sur **Authentication** (Authentification).

    ![Administrateur](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

8. Dans la page **Authentication** (Authentification), sélectionnez l’authentification unique **SAML 2**.

    ![Authentification unique](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

9. Dans la page **Authentication** (Authentification), effectuez les étapes suivantes :

    ![Authentification unique](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. Cliquez sur le bouton **Update IdP metadata** (Mettre à jour les métadonnées du fournisseur d’identité) situé en regard de l’option **Status** (État), puis collez le code XML de métadonnées que vous avez téléchargé à partir du portail Azure dans la zone de texte spécifiée.

    > [!Note]
    > Vous pouvez également valider les métadonnées du fournisseur d’identité via l’outil **Metadata validator**, comme dans la capture d’écran ci-dessus.

    b. Cliquez sur **Enregistrer**.
    
### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/skillsbase-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/skillsbase-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/skillsbase-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/skillsbase-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-skills-base-test-user"></a>Créer un utilisateur de test Skills Base

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Skills Base. Skills Base prend en charge le provisionnement juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Lorsque vous tentez d’accéder à Skills Base, si aucun utilisateur n’existe, Skills Base en crée un automatiquement.

>[!Note]
>Si vous avez besoin de créer un utilisateur manuellement, suivez les instructions [ici](http://wiki.skills-base.net/index.php?title=Adding_people_and_enabling_them_to_log_in).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique d’Azure en lui accordant l’accès à Skills Base.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Skills Base, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Skills Base**.

    ![Lien vers Skills Base dans la liste des applications](./media/skillsbase-tutorial/tutorial_skillsbase_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Skills Base dans le volet d’accès, vous êtes connecté automatiquement à votre application Skills Base.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillsbase-tutorial/tutorial_general_01.png
[2]: ./media/skillsbase-tutorial/tutorial_general_02.png
[3]: ./media/skillsbase-tutorial/tutorial_general_03.png
[4]: ./media/skillsbase-tutorial/tutorial_general_04.png

[100]: ./media/skillsbase-tutorial/tutorial_general_100.png

[200]: ./media/skillsbase-tutorial/tutorial_general_200.png
[201]: ./media/skillsbase-tutorial/tutorial_general_201.png
[202]: ./media/skillsbase-tutorial/tutorial_general_202.png
[203]: ./media/skillsbase-tutorial/tutorial_general_203.png

