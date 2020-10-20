---
title: 'Didacticiel : Intégration d’Azure Active Directory à Marketo | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 764f01fa5966a6620612405b4df37fc5ff44f33a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857909"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Didacticiel : Intégration d’Azure Active Directory à Marketo

Dans ce didacticiel, vous allez apprendre à intégrer Marketo à Azure Active Directory (Azure AD).
L’intégration de Marketo à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès Marketo.
* Vous pouvez permettre à vos utilisateurs d’être automatiquement connectés à Marketo (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour configurer l’intégration d’Azure AD à Marketo, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Marketo pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Marketo prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="adding-marketo-from-the-gallery"></a>Ajout de Marketo depuis la galerie

Pour configurer l’intégration de Marketo à Azure AD, vous devez ajouter Marketo, depuis la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Marketo depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Marketo**, sélectionnez **Marketo** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Marketo dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de Marketo, à l’aide d’un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Marketo associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Marketo, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique de Marketo](#configure-marketo-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer l’utilisateur de test Marketo](#create-marketo-test-user)** pour avoir dans Marketo un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de Marketo, effectuez les étapes suivantes :

1. Sur le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Marketo**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, effectuez les étapes suivantes :

    ![Informations d’authentification unique relatives au domaine et aux URL Marketo](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://saml.marketo.com/sp`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique de Marketo](https://investors.marketo.com/contactus.cfm). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Marketo**, copiez l’URL ou les URL appropriées, en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-marketo-single-sign-on"></a>Configurer l’authentification unique de Marketo

1. Pour obtenir l’ID Munchkin de votre application, connectez-vous à Marketo à l’aide des informations d’identification d’administrateur et procédez comme suit :
   
    a. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.
   
    b. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Capture d’écran montrant l’élément Admin sélectionné dans le volet de navigation.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Accédez au menu Intégration et cliquez sur le **lien Munchkin**.
   
    ![Capture d’écran montrant l’élément Munchkin sélectionné dans Integration.](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copiez l’ID Munchkin indiqué sur l’écran et complétez votre URL de réponse dans l’Assistant de configuration Azure AD.
   
    ![Capture d’écran affichant la page Munchkin dans laquelle vous pouvez copier l’ID de compte.](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Pour configurer l’authentification unique de l’application, suivez les étapes ci-dessous :
   
    a. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.
   
    b. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Capture d’écran montrant l’élément Admin sélectionné dans le volet de navigation.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Accédez au menu Intégration et cliquez sur **Authentification unique**.
   
    ![Capture d’écran montrant l’élément Single Sign-on sélectionné dans Integration.](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Pour activer les paramètres SAML, cliquez sur le bouton **Modifier**.
   
    ![Capture d’écran montrant la section SSO Settings, dans laquelle vous pouvez sélectionner EDIT.](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Activez** les paramètres d’authentification unique.
   
    f. Collez la valeur de **Identificateur Azure AD** dans la zone de texte **Issuer ID** (ID de l’émetteur).
   
    g. Dans la zone de texte **ID d’entité**, tapez l’URL `http://saml.marketo.com/sp`.
   
    h. Sélectionnez l’emplacement d’ID utilisateur en tant **qu’élément Identificateur de nom**.
   
    ![Capture d’écran montrant la section Edit SAML Settings dans laquelle vous pouvez indiquer les valeurs décrites.](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Si votre identificateur d’utilisateur n’est pas une valeur UPN, alors changez la valeur dans l’onglet Attribut.
   
    i. Chargez le certificat que vous avez téléchargé à partir de l’Assistant de configuration Azure AD. **Enregistrez** les paramètres.
   
    j. Modifiez les paramètres des pages de redirection.
   
    k. Collez la valeur de l’**URL de connexion** dans la zone de texte **Login URL** (URL de connexion).
   
    l. Collez la valeur de l’**URL de déconnexion** dans la zone de texte **Logout URL** (URL de déconnexion).
   
    m. Dans l’**URL d’erreur**, copiez l’**URL de votre instance Marketo**, puis cliquez sur le bouton **Enregistrer** pour enregistrer les paramètres.
   
    ![Capture d’écran montrant la boîte de dialogue Edit Redirect Pages dans laquelle vous pouvez entrer les valeurs décrites.](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Pour activer l’authentification unique pour les utilisateurs, procédez comme suit :
   
    a. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.
   
    b. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Capture d’écran montrant l’élément Admin sélectionné dans le volet de navigation.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Accédez au menu **Sécurité** et cliquez sur **Paramètres de connexion**.
   
    ![Capture d’écran affichant l’élément Login Settings sélectionné dans Security.](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Vérifiez l’option **Exiger l’authentification unique** et **enregistrez** les paramètres.
   
    ![Capture d’écran affichant la zone Password Strength Settings dans laquelle vous pouvez sélectionner l’option Require SSO.](./media/marketo-tutorial/tutorial_marketo_14.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Marketo.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Marketo**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Marketo**.

    ![Lien Marketo dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-marketo-test-user"></a>Créer l’utilisateur de test Marketo

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Marketo. Pour créer un utilisateur dans la plateforme Marketo, suivez ces étapes.

1. Connectez-vous à l’application Marketo à l’aide des informations d’identification de l’administrateur.

2. Dans le volet de navigation supérieur, cliquez sur le bouton **Administrateur**.
   
    ![Capture d’écran montrant l’élément Admin sélectionné dans le volet de navigation.](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Accédez au menu **Sécurité** et cliquez sur **Utilisateurs et rôles**
   
    ![Capture d’écran affichant l’élément Users & Roles sélectionné dans Security.](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Dans l’onglet Utilisateurs, cliquez sur le lien **Inviter un nouvel utilisateur**
   
    ![Capture d’écran montrant l’action Invite New User sous l’onglet Users.](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Dans l’Assistant d’invitation d’un nouvel utilisateur, renseignez les informations suivantes.
   
    a. Entrez l’adresse **e-mail** de l’utilisateur dans la zone de texte.
   
    ![Capture d’écran montrant la première étape de l’Assistant Invite New User, dans laquelle vous entrez les informations de l’utilisateur.](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Entrez le **prénom** dans la zone de texte.
   
    c. Entrez le **nom** dans la zone de texte
   
    d. Cliquez sur **Suivant**.

6. Dans l’onglet **Autorisations**, sélectionnez les **rôles d’utilisateur** et cliquez sur **Suivant**
   
    ![Capture d’écran montrant la première étape de l’Assistant Invite New User, dans laquelle vous indiquez des autorisations.](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Pour envoyer l’invitation d’utilisateur, cliquez sur le bouton **Envoyer**
   
    ![Capture d’écran montrant la première étape de l’Assistant Invite New User, dans laquelle vous entrez votre message.](./media/marketo-tutorial/tutorial_marketo_18.png)

8. L’utilisateur recevra une notification par e-mail. Pour activer le compte, il devra cliquer sur le lien et modifier le mot de passe. 

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Marketo dans le volet d’accès doit vous connecter automatiquement à l’application Marketo pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

