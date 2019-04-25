---
title: "Didacticiel : Intégration d'Azure Active Directory à Sectigo Certificate Manager | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a847ea34182994eb6465905e042bfa0218993491
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999159"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Didacticiel : Intégration d'Azure Active Directory à Sectigo Certificate Manager

L’objectif de ce tutoriel est de vous apprendre à intégrer Sectigo Certificate Manager à Azure Active Directory (Azure AD).
L’intégration de Sectigo Certificate Manager à Azure AD vous offre les avantages suivants :

* Vous pouvez contrôler dans Azure AD qui a accès à Sectigo Certificate Manager.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Sectigo Certificate Manager (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Sectigo Certificate Manager, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement à Sectigo Certificate Manager pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Sectigo Certificate Manager prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identité**

## <a name="adding-sectigo-certificate-manager-from-the-gallery"></a>Ajout de Sectigo Certificate Manager à partir de la galerie

Pour configurer l’intégration de Sectigo Certificate Manager à Azure AD, vous devez ajouter Sectigo Certificate Manager à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Sectigo Certificate Manager à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Sectigo Certificate Manager**, sélectionnez **Sectigo Certificate Manager** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Sectigo Certificate Manager dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Sectigo Certificate Manager via un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, l’utilisateur Azure AD et l’utilisateur Sectigo Certificate Manager correspondant doivent être associés.

Pour configurer et tester l’authentification unique d’Azure AD avec Sectigo Certificate Manager, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** pour configurer les paramètres d’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Sectigo Certificate Manager](#create-sectigo-certificate-manager-test-user)** pour avoir dans Sectigo Certificate Manager un équivalent de Britta Simon lié à la représentation de l’utilisateur Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Sectigo Certificate Manager, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), à la page d’intégration de l’application **Sectigo Certificate Manager**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    ![Informations d’authentification unique pour le domaine et les URL de Sectigo Certificate Manager](common/idp-relay.png)

    a. Dans la zone de texte **Identificateur**, tapez l’une des URL :
    
    | |
    |--|
    | `https://cert-manager.com/shibboleth` |
    | `https://hard.cert-manager.com/shibboleth` |

    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL :

    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/SAML2/POST` |
    | `https://hard.cert-manager.com/Shibboleth.sso/SAML2/POST` |

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **État de relais**, tapez l’une des URL :
    
    | |
    |--|
    | `https://cert-manager.com/customer/SSLSupport/idp` |
    | `https://hard.cert-manager.com/customer/SSLSupport/idp` |

5.  Si vous voulez configurer l’application en mode lancé par le **fournisseur de service**, procédez comme suit :

    ![Informations d’authentification unique pour le domaine et les URL de Sectigo Certificate Manager](common/both-signonurl.png)

    Dans la zone de texte **URL d’authentification unique**, tapez l’une des URL :
    
    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/Login`|
    | `https://hard.cert-manager.com/Shibboleth.sso/Login`|

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

7. Dans la section **Configurer Sectigo Certificate Manager**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurer l’authentification unique de Sectigo Certificate Manager

Pour configurer l’authentification unique côté **Sectigo Certificate Manager**, vous devez envoyer le **certificat (Base64)** téléchargé et les URL copiées appropriées du portail Azure à l’[équipe du support technique Sectigo Certificate Manager](https://sectigo.com/support). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`. Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sectigo Certificate Manager.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Sectigo Certificate Manager**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Sectigo Certificate Manager**.

    ![Lien Sectigo Certificate Manager dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-sectigo-certificate-manager-test-user"></a>Créer utilisateur de test Sectigo Certificate Manager

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Sectigo Certificate Manager. Collaborez avec  [l’équipe de support technique Sectigo Certificate Manager](https://sectigo.com/support) pour ajouter les utilisateurs à la plateforme Sectigo Certificate Manager. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Sectigo Certificate Manager dans le panneau d’accès, vous devez être automatiquement connecté à l’application Sectigo Certificate Manager pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

