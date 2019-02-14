---
title: 'Didacticiel : Intégration d’Azure Active Directory à Ivanti Service Manager (ISM) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Ivanti Service Manager (ISM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8859c0363a738c744f5c9b496cf77f5aaf1ba61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203503"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Tutoriel : Intégration d’Azure Active Directory à Ivanti Service Manager (ISM)

L’objectif de ce tutoriel est de vous apprendre à intégrer Ivanti Service Manager (ISM) à Azure Active Directory (Azure AD).

L’intégration d’Ivanti Service Manager (ISM) à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Ivanti Service Manager (ISM).
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Ivanti Service Manager (ISM), par authentification unique, avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Ivanti Service Manager (ISM), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à Ivanti Service Manager (ISM) pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Ivanti Service Manager (ISM) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>Ajout d’Ivanti Service Manager (ISM) à partir de la galerie

Pour configurer l’intégration d’Ivanti Service Manager (ISM) à Azure AD, vous devez ajouter Ivanti Service Manager (ISM), disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Ivanti Service Manager (ISM) à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Ivanti Service Manager (ISM)**, sélectionnez **Ivanti Service Manager (ISM)** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Ivanti Service Manager (ISM) dans la liste des résultats](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Ivanti Service Manager (ISM) à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur Ivanti Service Manager (ISM) équivaut à un utilisateur Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Ivanti Service Manager (ISM) associé doit être établie.

Pour configurer et tester l’authentification unique d’Azure AD avec Ivanti Service Manager (ISM), vous devez effectuer les modules suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Ivanti Service Manager (ISM)](#creating-an-ivanti-service-manager-ism-test-user)** pour avoir un équivalent de Britta Simon dans Ivanti Service Manager (ISM) lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure, et configurer l’authentification unique dans votre application Ivanti Service Manager (ISM).

**Pour configurer l’authentification unique Azure AD avec Ivanti Service Manager (ISM), effectuez les tâches suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Ivanti Service Manager (ISM)**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial-general-301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL d’Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<customer>.saasit.com/` |
    | `https://<customer>.saasiteu.com/` |
    | `https://<customer>.saasitau.com/` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL d’Ivanti Service Manager (ISM)](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<customer>.saasit.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

6. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (brut)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-certificate.png) 

7. Dans la section **Configurer Ivanti Service Manager (ISM)**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration d’Ivanti Service Manager (ISM)](common/configuresection.png)

8. Pour configurer l’authentification unique côté **Ivanti Service Manager (ISM)**, vous devez envoyer le **Certificat (Base64)** téléchargé et les valeurs copiées **URL de connexion**, **Identificateur Azure AD** et **URL de déconnexion** à l’[équipe du support technique d’Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create-aaduser-01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create-aaduser-02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-an-ivanti-service-manager-ism-test-user"></a>Création d’un utilisateur de test Ivanti Service Manager (ISM)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Ivanti Service Manager (ISM). Ivanti Service Manager (ISM) prend en charge le provisionnement juste-à-temps (activé par défaut). Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé durant une tentative d’accès à Ivanti Service Manager (ISM).
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique d’Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Ivanti Service Manager (ISM).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Ivanti Service Manager (ISM)**.

    ![Configurer l'authentification unique](./media/ivanti-service-manager-tutorial/tutorial-ivanti-service-manager-app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Ivanti Service Manager (ISM) dans le volet d’accès, vous devez être connecté automatiquement à votre application Ivanti Service Manager (ISM).
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
