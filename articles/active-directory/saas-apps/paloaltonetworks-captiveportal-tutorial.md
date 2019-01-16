---
title: 'Tutoriel : Intégration d’Azure Active Directory à Palo Alto Networks - Captive Portal | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Palo Alto Networks - Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064727"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Tutoriel : Intégration d’Azure Active Directory à Palo Alto Networks - Captive Portal

Dans ce didacticiel, vous découvrez comment intégrer Palo Alto Networks - Captive Portal avec Azure Active Directory (Azure AD).
L’intégration de Palo Alto Networks - Captive Portal avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Palo Alto Networks - Captive Portal.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Palo Alto Networks - Captive Portal (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Palo Alto Networks - Captive Portal, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Palo Alto Networks - Captive Portal pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Palo Alto Networks - Captive Portal prend en charge l’authentification unique initiée par le **fournisseur d’identité**

* Palo Alto Networks - Captive Portal prend en charge l’attribution d’utilisateurs **juste-à-temps**.

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Ajout de Palo Alto Networks - Captive Portal depuis la galerie

Pour configurer l’intégration de Palo Alto Networks - Captive Portal avec Azure AD, vous devez ajouter Palo Alto Networks - Captive Portal à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Palo Alto Networks - Captive Portal à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Palo Alto Networks - Captive Portal**, sélectionnez **Palo Alto Networks - Captive Portal** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Palo Alto Networks - Captive Portal dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec Palo Alto Networks - Captive Portal sur un utilisateur de test nommé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Palo Alto Networks - Captive Portal associé.

Pour configurer et tester l’authentification unique Azure AD avec Palo Alto Networks - Captive Portal, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Palo Alto Networks - Captive Portal](#configure-palo-alto-networks---captive-portal-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Palo Alto Networks - Captive Portal](#create-palo-alto-networks---captive-portal-test-user)** pour obtenir un équivalent de Britta Simon dans Palo Alto Networks - Captive Portal lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Palo Alto Networks - Captive Portal, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Palo Alto Networks - Captive Portal**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la boîte de dialogue **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL de Palo Alto Networks - Captive Portal](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<Customer Firewall Hostname>/SAML20/SP`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Contactez l’[équipe de support client Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Configurer l’authentification unique Palo Alto Networks - Captive Portal

1. Ouvrez le site Palo Alto en tant qu’administrateur dans une autre fenêtre de navigateur.

2. Cliquez sur **Appareil**.

    ![Configurer l’authentification unique Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Sélectionnez **Fournisseur d’identité SAML** dans la barre de navigation gauche et cliquez sur « Importer » pour importer le fichier de métadonnées.

    ![Configurer l’authentification unique Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Effectuez les actions suivantes sur la fenêtre Importer

    ![Configurer l’authentification unique Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Dans la zone de texte **Nom du profil**, spécifiez un nom, par exemple Azure AD Admin UI.
    
    b. Dans **Métadonnées du fournisseur d’identité**, cliquez sur **Parcourir** et sélectionnez le fichier metadata.xml que vous avez téléchargé à partir du portail Azure.
    
    c. Cliquez sur **OK**

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

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Palo Alto Networks - Captive Portal.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Palo Alto Networks - Captive Portal**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Palo Alto Networks - Captive Portal**.

    ![Lien Palo Alto Networks - Captive Portal dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Créer un utilisateur de test Palo Alto Networks - Captive Portal

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Palo Alto Networks - Captive Portal. Palo Alto Networks - Captive Portal prend en charge l’**attribution d’utilisateurs juste-à-temps**, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Palo Alto Networks - Captive Portal, il en est créé un après l’authentification.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support client de Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Captive Portal est configuré derrière le pare-feu sur la machine virtuelle Windows. Pour tester l’authentification unique sur Captive Portal, ouvrez une session sur la machine virtuelle Windows avec RDP. À partir de la session RDP, ouvrez un navigateur sur n’importe quel site web : il doit ouvrir automatiquement l’URL d’authentification unique et demander l’authentification. Une fois l’authentification terminée, vous devez être en mesure d’accéder aux sites web.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

