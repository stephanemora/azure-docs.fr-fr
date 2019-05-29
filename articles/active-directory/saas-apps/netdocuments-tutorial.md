---
title: 'Didacticiel : Intégration d’Azure Active Directory à NetDocuments | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: d42e266de9c958c8a71cc5fa680a78c2278cec6e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956776"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Didacticiel : Intégration d’Azure Active Directory à NetDocuments

Dans ce didacticiel, vous allez apprendre à intégrer NetDocuments à Azure Active Directory (Azure AD).
L’intégration de NetDocuments dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à NetDocuments.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à NetDocuments (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec NetDocuments, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement NetDocuments pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* NetDocuments prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="adding-netdocuments-from-the-gallery"></a>Ajout de NetDocuments à partir de la galerie

Pour configurer l’intégration de NetDocuments à Azure AD, vous devez ajouter NetDocuments à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter NetDocuments à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **NetDocuments**, sélectionnez **NetDocuments** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![NetDocuments dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec NetDocuments avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur NetDocuments associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec NetDocuments, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique NetDocuments](#configure-netdocuments-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test NetDocuments](#create-netdocuments-test-user)** pour obtenir un équivalent de Britta Simon dans NetDocuments lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec NetDocuments, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **NetDocuments**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL NetDocuments](common/sp-reply.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. L’ID de dépôt est une valeur commençant par l’autorité de certification suivie du code à 8 caractères associé à votre dépôt NetDocuments. Pour plus d’informations, consultez le [document sur la prise en charge des identités fédérées NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login). Vous pouvez également contacter l’[équipe de support technique de NetDocuments](https://support.netdocuments.com/hc/) pour obtenir ces valeurs si vous rencontrez des difficultés lorsque vous effectuez la configuration en utilisant les informations ci-dessus. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer NetDocuments**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-netdocuments-single-sign-on"></a>Configurer l’authentification unique NetDocuments

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise NetDocuments en tant qu’administrateur.

2. Accédez à **Admin**.

3. Cliquez sur **Add and remove users and groups**.
   
    ![Référentiel](./media/netdocuments-tutorial/ic795047.png "Référentiel")

4. Cliquez sur **Configure advanced authentication options**.
    
    ![Configurer les options d’authentification avancées](./media/netdocuments-tutorial/ic795048.png "Configurer les options d’authentification avancées")

5. Dans la boîte de dialogue **Identité fédérée**, procédez comme suit :
   
    ![Identité fédérée](./media/netdocuments-tutorial/ic795049.png "Identité fédérée")
   
    a. Pour **Federated identity server type**, sélectionnez **Active Directory Federation Services**.
   
    b. Cliquez sur **Choisir un fichier**, pour importer le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.
   
    c. Cliquez sur **OK**.

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
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à NetDocuments.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **NetDocuments**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **NetDocuments**.

    ![Le lien NetDocuments dans la liste Applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-netdocuments-test-user"></a>Créer un utilisateur de test NetDocuments

Pour permettre aux utilisateurs Azure AD de se connecter à NetDocuments, vous devez les configurer dans NetDocuments.  
Dans le cas de NetDocuments, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **NetDocuments** en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Admin**.
   
    ![Administrateur](./media/netdocuments-tutorial/ic795051.png "Administrateur")

3. Cliquez sur **Add and remove users and groups**.
   
    ![Référentiel](./media/netdocuments-tutorial/ic795047.png "Référentiel")

4. Dans la zone de texte **Email Address**, tapez l’adresse e-mail d’un compte Azure Active Directory valide à approvisionner, puis cliquez sur **Add User**.
   
    ![Adresse de messagerie](./media/netdocuments-tutorial/ic795053.png "adresse de messagerie")
   
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé. Vous pouvez utiliser n’importe quels outils ou API de création de compte utilisateur, fournis par NetDocuments, pour approvisionner des comptes utilisateur Azure Active Directory.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette NetDocuments dans le panneau d’accès doit vous connecter automatiquement à l’application NetDocuments pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

