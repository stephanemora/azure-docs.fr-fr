---
title: "Tutoriel : Intégration d'Azure Active Directory à Wdesk | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985507"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Wdesk

Dans ce tutoriel, vous allez découvrir comment intégrer Wdesk à Azure Active Directory (Azure AD). Quand vous intégrez Wdesk à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Wdesk.
* Permettre aux utilisateurs de se connecter automatiquement à Wdesk avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Wdesk pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Wdesk prend en charge l’authentification unique lancée par **le fournisseur de services** et le **fournisseur d’identité**
* Après avoir configuré Wdesk, vous pouvez appliquer des contrôles de session qui protègent l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Les contrôles de session sont étendus à partir de l’accès conditionnel. [Découvrir comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Ajout de Wdesk depuis la galerie

Pour configurer l’intégration de Wdesk avec Azure AD, vous devez ajouter Wdesk à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Wdesk** dans la zone de recherche.
1. Sélectionnez **Wdesk** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de Wdesk, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Wdesk associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Wdesk, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Wdesk](#configure-wdesk-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Wdesk](#create-wdesk-test-user)** pour avoir un équivalent de B.Simon dans Wdesk lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès de Wdesk, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Wdesk**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Wdesk](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Wdesk](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous obtenez ces valeurs depuis le portail WDesk lorsque vous configurez l’authentification unique.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Wdesk**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez brittasimon@yourcompanydomain.extension. Par exemple : BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Wdesk.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Wdesk**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Wdesk**.

    ![Lien Wdesk dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-wdesk-sso"></a>Configurer l’authentification unique Wdesk

1. Ouvrez une autre fenêtre de navigateur web, puis connectez-vous à Wdesk en tant qu’administrateur de la sécurité.

2. En bas à gauche, cliquez sur **administrateur** et sélectionnez **Administrateur de compte** :
 
     ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Dans Administrateur Wdesk, accédez à **Sécurité**, puis **SAML** > **Paramètres SAML** :

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Dans **Paramètres généraux**, cochez l’option **Activer l’authentification unique SAML** :

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Dans **Détails sur le fournisseur de services**, procédez comme suit :

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copiez l’**URL de connexion** et collez-la dans la zone de texte **URL d’authentification** sur le portail Azure.
   
      b. Copiez l’**URL de métadonnée** et collez-la dans la zone de texte **Identificateur** sur le portail Azure.
       
      c. Copiez l’**URL de consommateur** et collez-la dans la zone de texte **URL de réponse** sur le portail Azure.
   
      d. Cliquez sur **Enregistrer** sur le portail Azure pour enregistrer les modifications.      

6. Cliquez sur **Configurer les paramètres du fournisseur d’identité** pour ouvrir la boîte de dialogue **Modifier les paramètres du fournisseur d’identité**. Cliquez sur **Choisir un fichier** pour trouver le fichier **Metadata.xml** que vous avez enregistré depuis le portail, puis chargez-le.
    
    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Cliquez sur **Save changes**.

    ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Créer un utilisateur de test Wdesk

Pour se connecter à Wdesk, les utilisateurs d’Azure AD doivent être provisionnés dans Wdesk. Dans Wdesk, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Wdesk en tant qu’administrateur de la sécurité.

2. Accédez à **Administrateur** > **Compte Administrateur**.

     ![Configure Single Sign-On](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Dans **Personnes**, cliquez sur **Membres**.

4. Cliquez maintenant sur **Ajouter membre** pour ouvrir la boîte de dialogue **Ajouter membre**. 
   
    ![Création d’un utilisateur de test Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. Dans la zone de texte **Utilisateur**, entrez un nom d’utilisateur, par exemple brittasimon@contoso.com, puis cliquez sur le bouton **Continuer**.

    ![Création d’un utilisateur de test Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Renseignez les détails comme indiqué ci-dessous :
  
    ![Création d’un utilisateur de test Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. Dans la zone de texte **E-mail**, entrez une adresse e-mail d’utilisateur, par exemple brittasimon@contoso.com.

    b. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la zone de texte **Nom**, entrez le nom de l’utilisateur, par exemple **Simon**.

7. Cliquez sur **Enregistrer membre**.  

    ![Création d’un utilisateur de test Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Wdesk dans le volet d’accès, vous devez être connecté automatiquement à l’application Wdesk pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)