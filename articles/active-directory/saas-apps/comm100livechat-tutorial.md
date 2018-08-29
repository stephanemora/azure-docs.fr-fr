---
title: 'Didacticiel : Intégration d’Azure Active Directory à Comm100 Live Chat | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42141463"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Didacticiel : Intégration d’Azure Active Directory à Comm100 Live Chat

Dans ce didacticiel, vous allez apprendre à intégrer Comm100 Live Chat à Azure Active Directory (Azure AD).

L’intégration de Comm100 Live Chat à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Comm100 Live Chat.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Comm100 Live Chat (au moyen de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Comm100 Live Chat, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Comm100 Live Chat pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Comm100 Live Chat à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Ajout de Comm100 Live Chat à partir de la galerie
Pour configurer l’intégration de Comm100 Live Chat avec Azure AD, vous devez ajouter Comm100 Live Chat à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Comm100 Live Chat à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Comm100 Live Chat**, sélectionnez **Comm100 Live Chat** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Comm100 Live Chat dans la liste des résultats](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Comm100 Live Chat avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans Comm100 Live Chat équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation de lien entre un utilisateur Azure AD et un utilisateur Comm100 Live Chat associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Comm100 Live Chat, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Comm100 Live Chat](#create-a-comm100-live-chat-test-user)** pour avoir un équivalent de Britta Simon dans Comm100 Live Chat lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application Comm100 Live Chat.

**Pour configurer l’authentification unique Azure AD avec Comm100 Live Chat, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Comm100 Live Chat**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. Dans la section **Comm100 Live Chat Domain and URLs** (Domaine et URL Comm100 Live Chat), procédez comme suit :

    ![Informations d’authentification unique dans la section Comm100 Live Chat Domain and URLs (Domaine et URL Comm100 Live Chat)](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Vous mettrez à jour cette valeur avec l’URL de connexion réelle. La procédure est expliquée plus loin dans le didacticiel.

4. L’application Comm100 Live Chat s’attend à ce que les instructions d’assertion SAML contiennent des attributs spécifiques. Configurez les attributs suivants pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

    ![Configure Single Sign-On](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    |  Nom de l'attribut  | Valeur de l’attribut |
    | --------------- | -------------------- |    
    |   email    | user.mail |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.

6. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. Dans la section **Comm100 Live Chat Configuration** (Configuration de Comm100 Live Chat), cliquez sur **Configure Comm100 Live Chat** (Configurer Comm100 Live Chat) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Comm100 Live Chat en tant qu’administrateur de la sécurité.

10. Dans l’angle supérieur droit de la page, cliquez sur **My Account** (Mon compte).

    ![Comm100 Live Chat : myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. Dans le menu, à gauche, cliquez sur **Security** (Sécurité), puis sur **Agent Single Sign-On** (Authentification unique des agents).

    ![Comm100 Live Chat : sécurité](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Dans la page **Agent Single Sign-On** (Authentification unique des agents), effectuez les opérations suivantes :

    ![Comm100 Live Chat : sécurité](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Copiez le premier lien en surbrillance et collez-le dans la zone de texte **URL de connexion** de la section **Comm100 Live Chat Domain and URLs** (Domaine et URL Comm100 Live Chat) du Portail Azure.

    b. Dans la zone de texte **SAML SSO URL** (URL SSO SAML), collez la valeur de **SAML Single Sign-On Service URL** (l’URL du service d’authentification unique SAML) que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **URL de déconnexion à distance**, collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du Portail Azure.

    d. Sous **Certificat**, cliquez sur **Choose a File** (Choisir un fichier) pour charger le certificat codé en base 64 que vous avez téléchargé à partir du Portail Azure.

    e. Cliquez sur **Enregistrer les modifications**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-comm100-live-chat-test-user"></a>Créer un utilisateur de test Comm100 Live Chat

Pour permettre aux utilisateurs d’Azure AD de se connecter à Comm100 Live Chat, vous devez les approvisionner dans Comm100 Live Chat. Dans Comm100 Live Chat, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Comm100 Live Chat en tant qu’administrateur de la sécurité.

2. Dans l’angle supérieur droit de la page, cliquez sur **My Account** (Mon compte).

    ![Comm100 Live Chat : myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Dans le menu, à gauche, cliquez sur **Agents**, puis sur **New Agent** (Nouvel agent).

    ![Comm100 Live Chat : agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Dans la page **New Agent** (Nouvel agent), effectuez les opérations suivantes :

    ![Comm100 Live Chat : nouvel agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Dans la zone de texte **Email** (E-mail), entrez l’adresse e-mail de l’utilisateur, par exemple **Brittasimon@contoso.com**.

    b. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la zone de texte **Last Name** (Nom), saisissez le nom de famille de l’utilisateur, par exemple **Simon**.

    d. Dans la zone de texte **Display Name** (Nom d’affichage), tapez le nom d’affichage de l’utilisateur, par exemple, **Britta Simon**.

    e. Dans la zone de texte **Password** (Mot de passe), tapez votre mot de passe.

    f. Cliquez sur **Enregistrer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Comm100 Live Chat.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Comm100 Live Chat, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Comm100 Live Chat**.

    ![Lien Comm100 Live Chat dans la liste des applications](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Comm100 Live Chat dans le volet d’accès, vous êtes connecté automatiquement à votre application Comm100 Live Chat.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

