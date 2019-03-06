---
title: 'Tutoriel : Intégration d’Azure Active Directory à PureCloud by Genesys | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: c9b2770f861098993623d69f6b9f6a1577c9cf27
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890540"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Tutoriel : Intégration d’Azure Active Directory à PureCloud by Genesys

Dans ce tutoriel, vous allez apprendre à intégrer PureCloud by Genesys à Azure Active Directory (Azure AD).
L’intégration de PureCloud by Genesys à Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à PureCloud by Genesys.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à PureCloud by Genesys (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec PureCloud by Genesys, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement PureCloud by Genesys pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* PureCloud by Genesys prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Ajout de PureCloud by Genesys à partir de la galerie

Pour configurer l’intégration de PureCloud by Genesys dans Azure AD, vous devez ajouter PureCloud by Genesys à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter PureCloud by Genesys à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **PureCloud by Genesys**, sélectionnez **PureCloud by Genesys** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![PureCloud by Genesys dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec PureCloud by Genesys pour un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur PureCloud by Genesys associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec PureCloud by Genesys, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique PureCloud by Genesys](#configure-purecloud-by-genesys-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** pour avoir un équivalent de Britta Simon dans PureCloud by Genesys lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec PureCloud by Genesys, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **PureCloud by Genesys**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique de domaine et d’URL PureCloud by Genesys](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL appropriée pour votre région :
    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Dans la zone de texte **URL de réponse**, tapez une URL appropriée pour votre région :
    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique de domaine et d’URL PureCloud by Genesys](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL appropriée pour votre région :
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. Votre application PureCloud by Genesys attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône  **Modifier** pour ouvrir la boîte de dialogue  **Attributs utilisateur** .

    ![image](common/edit-attribute.png)

7. Outre ceux mentionnés ci-dessus, l’application PureCloud by Genesys s’attend également à ce que quelques attributs de plus soient transmis dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

    | Nom | Attribut source|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

9. Dans la section **Configurer PureCloud by Genesys**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Configurer l’authentification unique PureCloud by Genesys

1. Dans une autre fenêtre de navigateur web, connectez-vous à PureCloud by Genesys en tant qu’administrateur.

2. Cliquez sur **Admin** (Administrateur) en haut de la fenêtre, puis accédez à **Single Sign-on** (Authentification unique) sous **Integrations** (Intégrations).

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Accédez à l’onglet **ADFS/Azure AD (Premium)** et effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Dans **ADFS Certificate** (Certificat ADFS), cliquez sur **Browse** (Parcourir) pour charger le certificat codé en base 64 que vous avez téléchargé du portail Azure.

    b. Dans la zone de texte **ADFS Issuer URI** (URI de l’émetteur SAML), collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Target URI** (URI cible), collez la valeur **URL de connexion** que vous avez copiée du portail Azure.

    d. Pour la valeur **Relying Party Identifier** (Identificateur de partie de confiance), accédez au portail Azure et, dans la page d’intégration de l’application **PureCloud by Genesys**, cliquez sur l’onglet **Propriétés** et copiez la valeur **ID de l’application**. Collez ensuite cette valeur dans la zone de texte **Relying Party Identifier**. 

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Cliquez sur **Enregistrer**.   

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à PureCloud by Genesys.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **PureCloud by Genesys**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **PureCloud by Genesys**.

    ![Lien PureCloud by Genesys dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-purecloud-by-genesys-test-user"></a>Créer un utilisateur de test PureCloud by Genesys

Pour permettre aux utilisateurs Azure AD de se connecter à PureCloud by Genesys, vous devez les provisionner dans PureCloud by Genesys. Dans PureCloud by Genesys, le provisionnement se fait manuellement.

**Pour provisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à PureCloud by Genesys en tant qu’administrateur.

2. Cliquez sur **Admin** (Administrateur) en haut de la fenêtre, puis accédez à la section **People** (Personnes) sous **People & Permissions** (Personnes & Autorisations).

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure03.png)

3. Dans la page People, cliquez sur **Add Person** (Ajouter une personne).

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure04.png)

4. Dans la fenêtre contextuelle **Add People to the Organization** (Ajouter des personnes à l’organisation), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Dans la zone de texte **Full Name** (Nom complet), entrez le nom d’un utilisateur, par exemple **Brittasimon**.

    b. Dans la zone de texte **Email** (E-mail), entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.
    
    c. Cliquez sur **Créer**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette PureCloud by Genesys dans le volet d’accès, vous devez être connecté automatiquement à l’application PureCloud by Genesys pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

