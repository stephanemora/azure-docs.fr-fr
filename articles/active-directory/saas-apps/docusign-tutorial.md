---
title: 'Tutoriel : Intégration d’Azure Active Directory avec DocuSign | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35fc4e855ef53bd7e667fc8de7146916b4d05acb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407081"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutoriel : Intégration d’Azure Active Directory avec DocuSign

Dans ce didacticiel, vous allez apprendre à intégrer DocuSign à Azure Active Directory (Azure AD).
L’intégration de DocuSign à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à DocuSign.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Sugar CRM (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec DocuSign, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement DocuSign pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* DocuSign prend en charge l’authentification unique lancée par le **fournisseur de services**

* DocuSign prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="adding-docusign-from-the-gallery"></a>Ajout de DocuSign à partir de la galerie

Pour configurer l’intégration de DocuSign à Azure AD, vous devez ajouter DocuSign à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter DocuSign à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **DocuSign**, sélectionnez **DocuSign** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![DocuSign dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec DocuSign à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur DocuSign associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec DocuSign, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification DocuSign](#configure-docusign-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur test DocuSign](#create-docusign-test-user)** pour avoir un équivalent de Britta Simon dans DocuSign lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec DocuSign, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **DocuSign**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations relatives à l’authentification unique des URL et du domaine DocuSign](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant : `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. La procédure est expliquée ultérieurement dans le tutoriel, dans la section **Afficher les points de terminaison SAML 2.0**.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer DocuSign**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-docusign-single-sign-on"></a>Configurer l’authentification unique DocuSign

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration DocuSign** en tant qu’administrateur.

2. Dans l’angle supérieur droit, cliquez sur **l’icône de profil**, puis cliquez sur **Accéder à Admin**.
  
    ![Configuration de l'authentification unique][51]

3. Dans la page des solutions de votre domaine, cliquez sur **Domaines**.

    ![Configuration de l'authentification unique][50]

4. Dans la section **Domaines**, cliquez sur **REVENDIQUER LE DOMAINE**.

    ![Configuration de l'authentification unique][52]

5. Dans la boîte de dialogue de **revendication du domaine**, dans la zone de texte **Nom du domaine**, indiquez votre domaine d’entreprise, puis cliquez sur **REVENDIQUER**. Veillez à vérifier le domaine et assurez-vous que son état est actif.

    ![Configuration de l'authentification unique][53]

6. Dans la page des solutions de votre domaine, cliquez sur **Fournisseurs d'identité**.
  
    ![Configuration de l'authentification unique][54]

7. Dans la section **Fournisseurs d’identité**, cliquez sur **AJOUTER UN FOURNISSEUR D’IDENTITÉ**. 

    ![Configuration de l'authentification unique][55]

8. Dans la page **Identity Provider Settings** (Paramètres du fournisseur d’identité), effectuez les actions suivantes :

    ![Configuration de l'authentification unique][56]

    a. Dans la zone de texte **Nom** , entrez le nom de votre configuration. N’utilisez pas d’espaces.

    b. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur de l’**identificateur Azure AD** que vous avez copié dans le portail Azure.

    c. Dans la zone de texte **URL de connexion du fournisseur d'identité**, collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    d. Dans la zone de texte **URL de déconnexion du fournisseur d'identité**, collez la valeur de l’**URL de déconnexion** que vous avez copiée dans le portail Azure.

    e. Sélectionnez **Signer la demande d’authentification**.

    f. Sous **Send AuthN request by** (Envoyer la demande d’authentification par), sélectionnez **POST**.

    g. Sous **Send logout request by** (Envoyer la demande de déconnexion par), sélectionnez **GET**.

    h. Dans la section **Mappage des attributs personnalisés**, cliquez sur **AJOUTER UN NOUVEAU MAPPAGE**.

    ![Configuration de l'authentification unique][62]

    i. Choisissez le champ que vous voulez mapper avec la revendication Azure AD. Dans cet exemple, la revendication **emailaddress** est mappée sur la valeur de **https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Il s’agit du nom de revendication par défaut d’Azure AD pour la revendication e-mail. Ensuite, cliquez sur **ENREGISTRER**.

    ![Configuration de l'authentification unique][57]

    > [!NOTE]
    > Utilisez l’**identificateur d’utilisateur** approprié pour mapper l’utilisateur d’Azure AD au mappage utilisateur DocuSign. Sélectionnez le champ correct et entrez la valeur appropriée en fonction des paramètres de votre organisation.

    j. Dans la section **Certificats du fournisseur d'identité**, cliquez sur **AJOUTER UN CERTIFICAT**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure AD avant de cliquer sur **ENREGISTRER**.

    ![Configuration de l'authentification unique][58]

    k. Dans la section **Fournisseurs d'identité**, cliquez sur **ACTIONS**, puis sur **Points de terminaison**.

    ![Configuration de l'authentification unique][59]

    l. Dans la section **View SAML 2.0 Endpoints** (Afficher les points de terminaison SAML 2.0) du **portail d’administration DocuSign**, effectuez les étapes suivantes :

    ![Configuration de l'authentification unique][60]

    * Copiez la valeur **Service Provider Issuer URL** (URL de l’émetteur du fournisseur d’identité) et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    * Copiez la valeur **Service Provider Login URL** (URL de connexion du fournisseur de services) et collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

    * Cliquez sur **Fermer**

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez brittasimon@yourcompanydomain.extension. Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à DocuSign.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **DocuSign**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **DocuSign**.

    ![Lien DocuSign dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-docusign-test-user"></a>Créer un utilisateur de test DocuSign

Dans cette section, un utilisateur appelé Britta Simon est créé dans DocuSign. DocuSign prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans DocuSign, il est créé après l’authentification.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette DocuSign dans le panneau d’accès doit vous connecter automatiquement à l’application DocuSign pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
