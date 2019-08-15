---
title: 'Didacticiel : Intégration d’Azure Active Directory à EBSCO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35cb408473da8c6397c5034ae20ac0a50b0953ea
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944720"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Didacticiel : Intégration d’Azure Active Directory à EBSCO

Dans ce didacticiel, vous allez apprendre à intégrer EBSCO à Azure Active Directory (Azure AD).
L’intégration d’EBSCO à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à EBSCO.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à EBSCO (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à EBSCO, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement EBSCO pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* EBSCO prend en charge l’authentification unique lancée par le **fournisseur de services** et le **fournisseur d’identité**

* EBSCO prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="adding-ebsco-from-the-gallery"></a>Ajout d’EBSCO depuis la galerie

Pour configurer l’intégration d’EBSCO à Azure AD, vous devez ajouter EBSCO, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter EBSCO à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **EBSCO**, sélectionnez **EBSCO** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![EBSCO dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès d’EBSCO, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur EBSCO associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec EBSCO, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique EBSCO](#configure-ebsco-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test EBSCO](#create-ebsco-test-user)** pour avoir dans EBSCO un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD auprès d’EBSCO, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **EBSCO**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez l’étape suivante :

    ![Informations d’authentification unique dans Domaine et URL EBSCO](common/idp-identifier.png)

    Dans la zone de texte **Identificateur**, tapez une URL :  `pingsso.ebscohost.com`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![image](common/both-preintegrated-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez l’[équipe du support EBSCO](mailto:sso@ebsco.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

    o **Éléments uniques :**  

    o **Custid** = Entrez l’ID client EBSCO unique 

    o **Profile** = Les clients peuvent adapter le lien pour diriger les utilisateurs vers un profil spécifique (selon ce qu’ils achètent auprès d’EBSCO). Ils peuvent entrer un ID de profil spécifique. Les principaux ID sont eds (service de découverte EBSCO) et ehost (bases de données EBSOCOhost). Vous trouverez des instructions les concernant [ici](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. L’application EBSCO s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

    ![image](common/edit-attribute.png)

     > [!Note]
    > L’attribut **name** est obligatoire et il est mappé sur la **valeur de l’identificateur de nom** dans l’application EBSCO. C’est ajouté par défaut pour vous éviter d’avoir à le faire manuellement.

7. En plus de ce qui précède, l’application EBSCO s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous : 

    | Nom | Attribut source|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **Enregistrer**.

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer EBSCO**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-ebsco-single-sign-on"></a>Configurer l’authentification unique EBSCO

Pour configurer l’authentification unique côté **EBSCO**, vous devez envoyer le **XML des métadonnées** téléchargé et les URL copiées correspondantes depuis le portail Azure à l’[équipe du support EBSCO](mailto:sso@ebsco.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EBSCO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **EBSCO**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **EBSCO**.

    ![Lien EBSCO dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-ebsco-test-user"></a>Créer un utilisateur de test EBSCO

Dans le cas d’EBSCO, l’attribution d’utilisateurs est automatique.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

Azure AD transmet les données requises à l’application EBSCO. L’attribution d’utilisateurs d’EBSCO peut être automatique ou exiger un formulaire à usage unique. Cela dépend si le client a un grand nombre de comptes EBSCOhost préexistants avec des paramètres personnels enregistrés. Vous pouvez en discuter avec [l’équipe de support technique d’EBSCO](mailto:sso@ebsco.com) pendant l’implémentation. Dans les deux cas, le client n’a pas besoin de créer de comptes EBSCOhost avant de procéder aux tests.

   >[!Note]
   >Vous pouvez automatiser la personnalisation et l’attribution d’utilisateurs d’EBSCOhost. Contactez [l’équipe de support technique d’EBSCO](mailto:sso@ebsco.com) concernant l’attribution d’utilisateurs juste-à-temps. 

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

1. Quand vous cliquez sur la vignette EBSCO dans le volet d’accès, vous devez être connecté automatiquement à votre application EBSCO.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

2. Une fois connecté à l’application, cliquez sur le bouton **Sign In** (Se connecter) dans l’angle supérieur droit.

    ![Connexion à EBSCO dans la liste des applications](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Vous recevez une invite à usage unique pour lier la connexion institutionnelle/SAML en choisissant l’option **Link your existing MyEBSCOhost account to your institution account now** (Lier maintenant votre compte MyEBSCOhost existant à votre compte institutionnel) ou **Create a new MyEBSCOhost account and link it to your institution account** (Créer un compte MyEBSCOhost et le lier à votre compte institutionnel). Le compte est utilisé pour la personnalisation de l’application EBSCOhost. Sélectionnez l’option de **création de compte** ; vous verrez alors que le formulaire pour la personnalisation est prérempli avec les valeurs de la réponse saml, comme indiqué dans la capture d’écran ci-dessous. Cliquez sur **Continue** (Continuer) pour enregistrer cette sélection.
    
     ![Utilisateur EBSCO dans la liste des applications](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Après avoir effectué la configuration ci-dessus, effacez les cookies et le cache, puis reconnectez-vous. Vous ne devrez pas vous reconnecter manuellement car les paramètres de personnalisation sont mémorisés.

## <a name="additional-sesources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

