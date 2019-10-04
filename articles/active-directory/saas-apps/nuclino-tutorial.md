---
title: 'Didacticiel : Intégration d’Azure Active Directory à Nuclino | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Nuclino.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: cce4857dd270f6868b8ae4d0de2117e46c20ce7c
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826307"
---
# <a name="tutorial-azure-active-directory-integration-with-nuclino"></a>Didacticiel : Intégration d’Azure Active Directory à Nuclino

Dans ce didacticiel, vous allez apprendre à intégrer Nuclino à Azure Active Directory (Azure AD).
L’intégration de Nuclino à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Nuclino.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Nuclino (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l'intégration d'Azure AD avec Nuclino, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Nuclino pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Nuclino prend en charge l’authentification unique initiée par le **fournisseur de services** et le **fournisseur d’identité**.

* Nuclino prend en charge l’approvisionnement d’utilisateurs **Juste-à-temps**.

## <a name="adding-nuclino-from-the-gallery"></a>Ajout de Nuclino à partir de la galerie

Pour configurer l’intégration de Nuclino dans Azure AD, vous devez ajouter Nuclino depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Nuclino à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Nuclino**, sélectionnez **Nuclino** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Nuclino dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Nuclino avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Nuclino associé doit être établie.

Pour configurer et tester l'authentification unique Azure AD avec Nuclino, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Nuclino](#configure-nuclino-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Nuclino](#create-nuclino-test-user)** pour avoir dans Nuclino un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Nuclino, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Nuclino**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Nuclino](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels à partir de la section **Authentification**, expliquée plus loin dans ce didacticiel.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Nuclino](common/metadata-upload-additional-signon.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez [l’équipe du support Nuclino](mailto:contact@nuclino.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. Votre application Nuclino s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Attributs d’utilisateur**.

    ![image](common/edit-attribute.png)

7. En plus de ce qui précède, l’application Nuclino s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous :

    | Nom |  Attribut source|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

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

9. Dans la section **Configurer Nuclino**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-nuclino-single-sign-on"></a>Configurer l’authentification unique Nuclino

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Nuclino en tant qu’administrateur.

2. Cliquez sur l’**icône**.

    ![Configuration de Nuclino](./media/nuclino-tutorial/configure1.png)

3. Cliquez sur l’**authentification unique Azure AD** et sélectionnez **Paramètres d’équipe** dans la liste déroulante.

    ![Configuration de Nuclino](./media/nuclino-tutorial/configure2.png)

4. Sélectionnez **Authentification** à partir du volet de navigation gauche.

    ![Configuration de Nuclino](./media/nuclino-tutorial/configure3.png)

5. Dans la section **Authentication**, procédez comme suit :

    ![Configuration de Nuclino](./media/nuclino-tutorial/configure4.png)

    a. Sélectionnez **Authentification unique SAML (SSO)** .

    b. Copiez la valeur **URL ACS (vous devez la copier et la coller dans votre fournisseur d’authentification unique)** et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** dans le portail Azure.

    c. Copiez la valeur **ID d’entité (vous devez la copier et la coller dans votre fournisseur d’authentification unique)** et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** dans le portail Azure.

    d. Dans la zone de texte **URL SSO**, collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    e. Dans la zone de texte **ID d’entité**, collez la valeur **Identificateur Azure AD** que vous avez copiée dans le portail Azure.

    f. Ouvrez votre **Certificat (Base64)** téléchargé dans le Bloc-notes. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.

    g. Cliquez sur **ENREGISTRER LES MODIFICATIONS**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Nuclino.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Nuclino**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Nuclino**.

    ![Lien Nuclino dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-nuclino-test-user"></a>Créer un utilisateur de test Nuclino

Dans cette section, un utilisateur nommé Britta Simon est créé dans Nuclino. Nuclino prend en charge l’approvisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Nuclino, il en est créé un après l’authentification.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Nuclino](mailto:contact@nuclino.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Nuclino dans le panneau d’accès doit vous connecter automatiquement à l’application Nuclino pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

