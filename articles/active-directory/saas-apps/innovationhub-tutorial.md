---
title: 'Tutoriel : Intégration d’Azure Active Directory avec Innovation Hub | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Innovation Hub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d72e4da0-0123-409b-96c2-e613f3f83fb1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 612cce99b1a510dfb53ec4a6c1e2a40185367db1
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36323624"
---
# <a name="tutorial-azure-active-directory-integration-with-innovation-hub"></a>Tutoriel : Intégration d’Azure Active Directory avec Innovation Hub

Dans ce tutoriel, vous allez apprendre à intégrer Innovation Hub avec Azure Active Directory (Azure AD).

L’intégration d’Innovation Hub avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Innovation Hub.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Innovation Hub (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Innovation Hub, vous aurez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Innovation Hub pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Innovation Hub à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-innovation-hub-from-the-gallery"></a>Ajouter Innovation Hub à partir de la galerie
Pour configurer l’intégration d’Innovation Hub à Azure AD, vous devrez ajouter Innovation Hub à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Innovation Hub à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Innovation Hub**, sélectionnez **Innovation Hub** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Innovation Hub dans la liste des résultats](./media/innovationhub-tutorial/tutorial_innovationhub_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Innovation Hub pour un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur Innovation Hub correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Innovation Hub associé.

Pour configurer et tester l’authentification unique Azure AD avec Innovation Hub, suivez les indications des sections ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Innovation Hub](#create-an-innovation-hub-test-user)** pour avoir un équivalent de Britta Simon dans Innovation Hub lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et configurer l’authentification unique dans votre application Innovation Hub.

**Pour configurer l'authentification unique Azure AD avec Innovation Hub, suivez les étapes ci-dessous :**

1. Sur la page d’intégration de l’application **Innovation Hub** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/innovationhub-tutorial/tutorial_innovationhub_samlbase.png)

3. Dans la section **Domaine et URL Innovation Hub**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Innovation Hub](./media/innovationhub-tutorial/tutorial_innovationhub_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<domainname>.innohb.com/auth/saml2/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<domainname>.innohb.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez [l’équipe de support client d’Innovation Hub](mailto:support@readify.net) pour obtenir ces valeurs.

4. L’application Innovation Hub attend les assertions SAML dans un certain format. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

    ![Configure Single Sign-On](./media/innovationhub-tutorial/attribute.png)

5. Dans la section **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** pour développer les attributs. Dans chacun des attributs affichés, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut | Valeur d'espace de noms|
    | ---------------| --------------- |----------------|
    | displayname | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/innovationhub-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/innovationhub-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Dans la liste **Valeur de l’espace de noms**, tapez la valeur de l’espace de noms indiquée pour cette ligne.

    e. Cliquez sur **OK**.

4. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/innovationhub-tutorial/tutorial_innovationhub_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/innovationhub-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté **Innovation Hub**, vous devrez envoyer **l’URL des métadonnées de fédération** copiée à [l’équipe de support d’Innovation Hub](mailto:support@readify.net). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/innovationhub-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/innovationhub-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/innovationhub-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/innovationhub-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-innovation-hub-test-user"></a>Créer un utilisateur de test Innovation Hub

L’objectif de cette section est de créer un utilisateur nommé Britta Simon dans Innovation Hub. Innovation Hub prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’en existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à Innovation Hub.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support d’Innovation Hub](mailto:support@readify.net).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Innovation Hub.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Innovation Hub, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Innovation Hub**.

    ![Lien Innovation Hub dans la liste Applications](./media/innovationhub-tutorial/tutorial_innovationhub_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Innovation Hub dans le volet d’accès, la connexion à votre application Innovation Hub doit se faire automatiquement.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
<!--Image references-->

[1]: ./media/innovationhub-tutorial/tutorial_general_01.png
[2]: ./media/innovationhub-tutorial/tutorial_general_02.png
[3]: ./media/innovationhub-tutorial/tutorial_general_03.png
[4]: ./media/innovationhub-tutorial/tutorial_general_04.png

[100]: ./media/innovationhub-tutorial/tutorial_general_100.png

[200]: ./media/innovationhub-tutorial/tutorial_general_200.png
[201]: ./media/innovationhub-tutorial/tutorial_general_201.png
[202]: ./media/innovationhub-tutorial/tutorial_general_202.png
[203]: ./media/innovationhub-tutorial/tutorial_general_203.png

