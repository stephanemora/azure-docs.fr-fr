---
title: 'Tutoriel : Intégration d’Azure Active Directory à dmarcian | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: jeedes
ms.openlocfilehash: 197e0cceb7457ffdd96ada7d4561de56c7ed10fc
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095649"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Tutoriel : Intégration d’Azure Active Directory à dmarcian

Dans ce didacticiel, vous allez apprendre à intégrer dmarcian à Azure Active Directory (Azure AD).

L’intégration de dmarcian à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à dmarcian.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à dmarcian (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à dmarcian, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement dmarcian pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de dmarcian à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-dmarcian-from-the-gallery"></a>Ajout de dmarcian à partir de la galerie
Pour configurer l’intégration de dmarcian à Azure AD, vous devez ajouter dmarcian à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter dmarcian à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **dmarcian**, sélectionnez **dmarcian** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![dmarcian dans la liste des résultats](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec dmarcian à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur dmarcian équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur dmarcian associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec dmarcian, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test dmarcian](#create-a-dmarcian-test-user)** pour avoir un équivalent de Britta Simon dans dmarcian lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application dmarcian.

**Pour configurer l’authentification unique Azure AD avec dmarcian, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **dmarcian**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. Dans la section **dmarcian Domain and URLs** (Domaines et URL dmarcian), suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans dmarcian Domain and URLs (Domaine et URL dmarcian)](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans dmarcian Domain and URLs (Domaine et URL dmarcian)](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. La procédure est expliquée plus loin dans le didacticiel. 

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. Dans une autre fenêtre de navigateur web, connectez-vous à dmarcian en tant qu’administrateur de la sécurité.

8. Cliquez sur **Profil** en haut à droite et accédez à **Préférences**.

    ![Les préférences ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Faites défiler, puis cliquez sur la section **Authentification unique** et sur **Configurer**.

    ![Authentification unique ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. Sur la page **Authentification unique SAML**, définissez **État** sur **Activé** et suivez les étapes ci-après :

    ![L’authentification ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Sous la section **Add dmarcian to your Identity Provider** (Ajouter dmarcian à votre fournisseur d’identité), cliquez sur  **COPY**  (Copier) pour copier l’**URL Assertion Consumer Service** de votre instance et collez-la dans la zone de texte  **URL de réponse** dans la section  **Domaine et URL dmarcian** du portail Azure.

    * Sous la section **Add dmarcian to your Identity Provider** (Ajouter dmarcian à votre fournisseur d’identité), cliquez sur  **COPIER**  (Copier) pour copier l’**ID d’entité** de votre instance et collez-la dans la zone de texte  **Identificateur** dans la section  **Domaine et URL dmarcian** du portail Azure.

    * Sous la section **Set up Authentication** (Configurer l’authentification), dans la zone de texte **Identity Provider Metadata** (Métadonnées du fournisseur d’identité), collez **l’URL des métadonnées de fédération d’application**, que vous avez copiée à partir du Portail Azure.

    * Sous la section **Set up Authentication** (Configurer l’authentification), dans la zone de texte **Attribute Statements** (Instructions d’attribut), collez l’URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * Sous la section **Set up Login URL** (Configurer l’URL de connexion), copiez l’**URL de connexion** de votre instance et collez-la dans la zone de texte  **URL de connexion** de la section  **Domaine et URL dmarcian** dans le Portail Azure.

        > [!Note]
        > Vous pouvez modifier l’**URL de connexion** en fonction de votre organisation.

    * Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/dmarcian-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/dmarcian-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-dmarcian-test-user"></a>Créer un utilisateur de test dmarcian

Pour se connecter à dmarcian, les utilisateurs Azure AD doivent être approvisionnés dans dmarcian. Dans dmarcian, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à dmarcian en tant qu’administrateur de la sécurité.

2. Cliquez sur **Profil** en haut à droite et accédez à **Gérer les utilisateurs**.

    ![L’utilisateur ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Sur le côté droit de la section **SSO Users** (Utilisateurs SSO), cliquez sur **Ajouter un nouvel utilisateur**.

    ![L’utilisateur ajouté ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Dans la fenêtre contextuelle **Ajouter un nouvel utilisateur**, procédez comme suit :

    ![Le nouvel utilisateur ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Dans la zone de texte **Nouvel e-mail d’utilisateur**, entrez l’adresse de messagerie de l’utilisateur, par exemple **brittasimon@contoso.com**.

    b. Si vous souhaitez accorder des droits d’administrateur à l’utilisateur, sélectionnez **Make User an Admin** (Rendre l’utilisateur administrateur).

    c. Cliquez sur **Add User**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à dmarcian.

![Attribuer le rôle utilisateur][200] 

**Pour assigner Britta Simon à dmarcian, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **dmarcian**.

    ![Lien dmarcian dans la liste des applications](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette dmarcian dans le volet d’accès, vous devez être connecté automatiquement à votre application dmarcian.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

