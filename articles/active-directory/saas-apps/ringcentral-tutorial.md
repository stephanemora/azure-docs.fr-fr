---
title: 'Didacticiel : Intégration d’Azure Active Directory à RingCentral | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 7cd20397c122834a23586bf51af6467f58531094
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997238"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Didacticiel : Intégration d’Azure Active Directory à RingCentral

Dans ce didacticiel, vous allez apprendre à intégrer RingCentral à Azure Active Directory (Azure AD).
L’intégration de RingCentral dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à RingCentral.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à RingCentral (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à RingCentral, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Un abonnement RingCentral pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* RingCentral prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="adding-ringcentral-from-the-gallery"></a>Ajout de RingCentral depuis la galerie

Pour configurer l’intégration de RingCentral à Azure AD, vous devez ajouter RingCentral à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter RingCentral à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **RingCentral**, sélectionnez **RingCentral** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![RingCentral dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec RingCentral, en vous servant d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, vous devez établir une relation entre l’utilisateur Azure AD et l’utilisateur RingCentral associé.

Pour configurer et tester l’authentification unique Azure AD avec RingCentral, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique RingCentral](#configure-ringcentral-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test RingCentral](#create-ringcentral-test-user)** pour avoir un équivalent de Britta Simon dans RingCentral, lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec RingCentral, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **RingCentral**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](common/upload-metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](common/browse-upload-metadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section **Configuration SAML de base**.

    ![Informations d’authentification unique dans Domaine et URL RingCentral](common/sp-identifier-reply.png)

    Dans la zone de texte **URL de connexion**, tapez une URL :
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > Sur la page de configuration de l’authentification unique de RingCentral, vous obtenez le **fichier de métadonnées du fournisseur de services**, qui sera expliqué plus tard dans le didacticiel.

5. Si vous n’avez pas de **fichier de métadonnées du fournisseur de services**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL d’authentification**, entrez une URL :

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Dans la zone de texte **Identificateur**, tapez une URL :

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant :

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![Informations d’authentification unique dans Domaine et URL RingCentral](common/sp-identifier-reply.png)

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>Configurer l’authentification unique RingCentral

1. Ouvrez une autre fenêtre de navigateur web et connectez-vous à RingCentral en tant qu’administrateur de la sécurité.

1. En haut de la page, cliquez sur **Outils**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Accédez à **Authentification unique**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Sur la page **Authentification unique**, dans la section **Configuration SSO**, cliquez sur **Modifier** dans l’**étape 1**, puis procédez comme suit :

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Sur la page **Configurer l’authentification unique**, effectuez les étapes suivantes :

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Cliquez sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    b. Après avoir chargé les métadonnées, les valeurs sont automatiquement renseignées dans la section **SSO General Information** (Informations générales sur l’authentification unique).

    c. Dans la section **Mappage d’attributs**, définissez **Map Email Attribute to** (Mapper l’attribut d’e-mail à) sur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Cliquez sur **Enregistrer**.

    e. Dans l’**étape 2**, cliquez sur **Télécharger** pour télécharger le **fichier de métadonnées du fournisseur de services**. Ensuite, chargez-le dans la section **Configuration SAML de base** afin de renseigner automatiquement les valeurs **Identificateur** et **URL de réponse** dans le portail Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Sur la même page, accédez à la section **Activer l’authentification unique** et procédez comme suit :

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Sélectionnez **Activer le service d’authentification unique**.

    * Sélectionnez **Allow users to log in with SSO or RingCentral credential** (Autoriser les utilisateurs à se connecter avec des informations d’identification de l’authentification unique ou RingCentral).

    * Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RingCentral.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **RingCentral**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **RingCentral**.

    ![Lien RingCentral dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-ringcentral-test-user"></a>Créer un utilisateur de test RingCentral

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans RingCentral. Collaborez avec l’ [équipe de support technique RingCentral](https://success.ringcentral.com/RCContactSupp)  pour ajouter les utilisateurs dans la plateforme RingCentral. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette RingCentral dans le volet d’accès, vous êtes connecté automatiquement à l’application RingCentral pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
