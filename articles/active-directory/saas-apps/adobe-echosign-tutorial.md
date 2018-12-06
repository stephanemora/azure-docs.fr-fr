---
title: 'Didacticiel : Intégration d’Azure Active Directory à Adobe Sign | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d5cdc2ec0c6cfcf52f84629485d0dd879fbf6fa2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053996"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Didacticiel : Intégration d’Azure Active Directory à Adobe Sign

Dans ce didacticiel, vous allez apprendre à intégrer Adobe Sign à Azure Active Directory (Azure AD).

L’intégration d’Adobe Sign dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adobe Sign.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Adobe Sign (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour plus d’informations sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Adobe Sign, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Adobe Sign pour lequel l’authentification unique est activée

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Adobe Sign à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-adobe-sign-from-the-gallery"></a>Ajouter Adobe Sign à partir de la galerie
Pour configurer l’intégration d’Adobe Sign à Azure AD, vous devez ajouter Adobe Sign depuis la galerie à votre liste d’applications SaaS gérées.

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Capture d’écran de l’icône d’Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Capture d’écran des menus d’Azure Active Directory, avec Applications d’entreprise et Toutes les applications mises en surbrillance][2]
    
3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Capture d’écran de l’option Nouvelle application en haut de la boîte de dialogue][3]

4. Dans la zone de recherche, tapez **Adobe Sign**.

    ![Capture d’écran de la zone de recherche](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Dans le volet de résultats, sélectionnez **Adobe signe**, puis **Ajouter**.

    ![Capture d’écran du panneau de résultats](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Adobe Sign au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit reconnaître une relation entre un utilisateur Azure AD et l’utilisateur lié dans Adobe signe.

Dans Adobe Sign, affectez la valeur du **nom d’utilisateur** indiquée dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Adobe Sign, suivez les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Adobe Sign](#creating-an-adobe-sign-test-user) pour avoir dans Adobe Sign un équivalent de Britta Simon qui soit associé à la représentation de l’utilisateur Azure AD.
4. [Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#testing-single-sign-on) pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Adobe Sign.

1. Dans le portail Azure, dans la page d’intégration de l’application **Adobe Sign**, cliquez sur **Authentification unique**.

    ![Capture d’écran de la page d’intégration de l’application Adobe Sign, avec l’authentification unique mise en surbrillance][4]

2. Dans la boîte de dialogue **Authentification unique**, zone **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Capture d’écran de la boîte de dialogue d’authentification unique, avec la zone Mode mise en surbrillance](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Dans la section **Domaine et URL Adobe Sign**, procédez comme suit :

    ![Capture d’écran de la section Domaine et URL Adobe Sign](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.echosign.com/`

    b. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html).

4. Dans la section **Certificat de signature SAML**, sélectionnez **Certificat(Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Capture d’écran de la section Certificat de signature SAML](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Capture d’écran du bouton Enregistrer](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration Adobe Sign**, sélectionnez **Configurer Adobe Sign** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**URL de déconnexion**, l’**ID d’entité SAML** et l’**URL du service d’authentification unique SAML** à partir de la section **Référence rapide**.

    ![Capture d’écran de la section Configuration d’Adobe Sign, avec Configurer Adobe Signer mis en surbrillance](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Avant la configuration, contactez [l’équipe de support client d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html) pour ajouter votre domaine à la liste verte dans Adobe Sign. Voici comment ajouter le domaine :

    a. L’[équipe de support client d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html) vous enverra un jeton généré aléatoirement. Pour votre domaine, le jeton sera dans ce format : **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publiez le jeton de vérification dans un enregistrement DNS texte et informez [l’équipe de support client d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Cela peut prendre plusieurs jours, voire plus. Notez que les retards de propagation DNS signifient qu’une valeur publiée dans le DNS peut ne pas être visible pendant une heure ou plus. Votre administrateur informatique doit savoir comment publier ce jeton dans un enregistrement DNS texte.
    
    c. Après que vous aurez averti [l’équipe de support client d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html) via le ticket de support, une fois le jeton publié, elle validera le domaine et l’ajoutera à votre compte.
    
    d. Il faut généralement procéder comme suit pour publier le jeton dans un enregistrement DNS :

    * Se connecter à son compte de domaine
    * Rechercher la page de mise à jour de l’enregistrement DNS. Cette page peut s’appeler Gestion DNS, Gestion des noms de serveur ou Paramètres avancés.
    * Trouver les enregistrements TXT pour votre domaine.
    * Ajouter un enregistrement TXT avec la valeur complète du jeton fournie par Adobe.
    * Enregistrez vos modifications.

8. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adobe Sign en tant qu’administrateur.

9. Dans le menu SAML, sélectionnez **Paramètres de compte** > **Paramètres SAML**.
   
    ![Capture d’écran de la page Paramètres SAML d’Adobe Sign](./media/adobe-echosign-tutorial/ic789520.png "Compte")

10. Dans la section **SAML Settings** (Paramètres SAML), procédez comme suit :
  
    ![Capture d’écran des paramètres SAML](./media/adobe-echosign-tutorial/ic789521.png "Paramètres SAML")
   
    a. Sous **Mode SAML**, sélectionnez **SAML obligatoire**.
   
    b. Sélectionnez **Autoriser les administrateurs de compte EchoSign à se connecter avec leurs informations d'identification EchoSign** .
   
    c. Sous **Création d’utilisateurs**, sélectionnez **Ajouter automatiquement les utilisateurs authentifiés via SAML**.

    d. Collez **l’ID d’entité SAML** que vous avez copié à partir du portail Azure dans la zone de texte **ID d’entité/URL d’émetteur**.
    
    e. Dans la zone de texte **URL de connexion/Point de terminaison SSO**, collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    f. Dans la zone de texte **URL de déconnexion/Point de terminaison SLO**, collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    g. Ouvrez votre **Certificat (Base64)** téléchargé dans le Bloc-notes. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat IdP**.

    h. Sélectionnez **Enregistrer les modifications**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Capture d’écran du nom de l’utilisateur test dans le portail Azure][100]

1. Dans le volet gauche du **portail Azure**, sélectionnez l’icône **Azure Active Directory**.

    ![Capture d’écran de l’icône Azure AD](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, et sélectionnez **Tous les utilisateurs**.
    
    ![Capture d’écran des menus Azure AD, avec Utilisateurs et groupes et Tous les utilisateurs mis en surbrillance](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter**.
 
    ![Capture d’écran du haut de la boîte de dialogue Tous les utilisateurs, avec l’option Ajouter mise en surbrillance](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Capture d’écran de la boîte de dialogue Utilisateur](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, saisissez l’adresse e-mail de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-adobe-sign-test-user"></a>Créer un utilisateur de test Adobe Sign

Pour pouvoir se connecter à Adobe Sign, les utilisateurs d’Azure AD doivent être attribués dans Adobe Sign. Il s’agit d’une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fourni par Adobe Sign pour approvisionner des comptes d’utilisateurs Azure AD. 

1. Connectez-vous à votre site d’entreprise **Adobe Sign** en tant qu’administrateur.

2. Dans le menu du haut, sélectionnez **Compte**. Puis, dans le volet gauche, sélectionnez **Utilisateurs et groupes** > **Créer un utilisateur**.
   
    ![Capture d’écran du site d’entreprise Adobe Sign, avec Compte, Utilisateurs et groupes et Créer un utilisateur mis en surbrillance](./media/adobe-echosign-tutorial/ic789524.png "Compte")
   
3. Dans la section **Create New User** (Créer un utilisateur), procédez comme suit :
   
    ![Capture d’écran de la section Créer un utilisateur](./media/adobe-echosign-tutorial/ic789525.png "Créer un utilisateur")
   
    a. Tapez l’**Adresse e-mail**, le **Prénom** et le **Nom** d’un compte Azure AD valide que vous souhaitez provisionner dans les zones de texte correspondantes.
   
    b. Sélectionnez **Créer un utilisateur**.

>[!NOTE]
>Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien pour confirmer le compte avant qu’il ne soit activé. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en l’autorisant à accéder à Adobe Sign.

![Capture d’écran de l’authentification unique du portail Azure][200] 

1. Dans le portail Azure, ouvrez la vue des applications. Accédez ensuite à la vue des répertoires, puis à **Applications d’entreprise**, et sélectionnez **Toutes les applications**.

    ![Capture d’écran de l’affichage des applications du portail Azure, avec Applications d’entreprise et Toutes les applications mises en surbrillance][201] 

2. Dans la liste des applications, sélectionnez **Adobe Sign**.

    ![Capture d’écran de la liste des applications, avec Adobe Sign mis en surbrillance](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Capture d’écran du menu, avec Utilisateurs et groupes mis en surbrillance][202] 

4. Sélectionnez **Ajouter**. Ensuite, dans la section **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Capture de la page Utilisateurs et groupes et de la section Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, dans la liste d’utilisateurs, sélectionnez **Britta Simon**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Lorsque vous cliquez sur la mosaïque Adobe Sign dans le panneau d’accès, vous devez automatiquement être connecté à votre application Adobe Sign. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
