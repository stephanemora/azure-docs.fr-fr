---
title: 'Didacticiel : Intégration d’Azure Active Directory à Fluxx Labs | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 5288ae3deaf82e76accb9c9584c250c7dbe2c9ca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Didacticiel : Intégration d’Azure Active Directory à Fluxx Labs

Dans ce didacticiel, vous allez apprendre à intégrer Fluxx Labs à Azure Active Directory (Azure AD).

L’intégration de Fluxx Labs à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Fluxx Labs.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Fluxx Labs (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Fluxx Labs, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Fluxx Labs pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Fluxx Labs à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-fluxx-labs-from-the-gallery"></a>Ajout de Fluxx Labs à partir de la galerie
Pour configurer l’intégration de Fluxx Labs à Azure AD, vous devez ajouter Fluxx Labs à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Fluxx Labs à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Fluxx Labs**, sélectionnez **Fluxx Labs** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Fluxx Labs dans la liste des résultats](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Fluxx Labs avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Fluxx Labs équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Fluxx Labs associé doit être établie.

Dans Fluxx Labs, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Fluxx Labs, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Fluxx Labs](#create-a-fluxx-labs-test-user)** pour avoir un équivalent de Britta Simon dans Fluxx Labs, lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application Fluxx Labs.

**Pour configurer l’authentification unique Azure AD avec Fluxx Labs, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Fluxx Labs**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. Dans la section **Domaine et URL Fluxx Labs**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :

    | Environnement | Modèle d’URL|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Pré-production | `https://<subdomain>.preprod.fluxxlabs.com`|
    | Staging    | `https://<subdomain>.stage.fluxxlabs.com`|
    
    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :

    | Environnement | Modèle d’URL|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-production | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
    | Staging    | `https://<subdomain>.stage.fluxxlabs.com/auth/saml/callback`|
    
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Fluxx Labs](mailto:travis@fluxxlabs.com).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Fluxx Labs**, cliquez sur **Configurer Fluxx Labs** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Fluxx Labs en tant qu’administrateur.

8. Dans le coin supérieur droit de la page, cliquez sur **l’icône Paramètres**, puis sur **Administration**.

    ![Configuration de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. Dans le volet Administration, sélectionnez **Plug-ins** > **Intégrations**, puis **SAML SSO-(Enabled)** (Authentification unique SAML-(Activée))

    ![Configuration de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. Dans la section d’attributs, procédez comme suit :
    
    ![Configuration de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. Cochez la case **SAML SSO** (Authentification unique SAML).

    b. Dans la zone de texte **Request Path** (Chemin d’accès à la demande), saisissez **/auth/saml**.

    c. Dans la zone de texte **Callback Path** (Chemin de rappel), saisissez **/auth/saml/callback**.

    d. Dans la zone de texte **URL Assertion Consumer Service**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du Portail Azure.

    e. Dans la zone de texte **Issuer** (Émetteur), collez la valeur **ID d’entité SAML** que vous avez copiée depuis le Portail Azure.

    f. Ouvrez votre certificat codé en base64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IDP Cert** (Certificat IDP).

    g.  Dans la zone de texte **Format de l’identificateur de nom**, saisissez la valeur `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Une fois le contenu enregistré, le champ apparaît vide pour des raisons de sécurité, mais la valeur est bien enregistrée dans la configuration.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Créer un utilisateur de test Fluxx Labs

Pour permettre aux utilisateurs Azure AD de se connecter à Fluxx Labs, vous devez les approvisionner dans Fluxx Labs. Dans le cas de Fluxx Labs, l’approvisionnement se fait manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Fluxx Labs en tant qu’administrateur.

2. Dans le tableau de bord, cliquez sur l’icône affichée ci-dessous pour ouvrir la carte **NEW PERSON** (NOUVELLE PERSONNE).

    ![Configuration de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

3. Dans la section **NEW PERSON** (NOUVELLE PERSONNE), procédez comme suit :
    
    ![Configuration de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. Dans la zone de texte **Identifiant de connexion**, entrez l’adresse e-mail de l’utilisateur, par exemple Azure_Admin.

    b. Dans la zone de texte **Mot de passe**, saisissez le mot de passe du compte Azure_Admin.

    c. Dans la zone de texte **Confirmer le mot de passe**, saisissez à nouveau le mot de passe du compte Azure_Admin.

    d. Fluxx Labs utilise une adresse e-mail comme identificateur unique pour les connexions avec authentification unique. Renseignez le champ **SSO UID** (UID SSO) avec l’adresse e-mail que l’utilisateur utilise pour la connexion avec authentification unique.

    e. Cliquez sur **Create Person** (Créer une personne).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Fluxx Labs.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Fluxx Labs, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Fluxx Labs**.

    ![Lien Fluxx Labs dans la liste des applications](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Fluxx Labs dans le volet d’accès, vous devez vous connecter automatiquement à votre application Fluxx Labs.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png

