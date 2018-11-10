---
title: 'Tutoriel : Intégration d’Azure Active Directory à Appinux | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Appinux.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f329341b-fb77-42e5-b6a6-0cd641d19670
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: a9b8e7738f2974b4c672af0e50e2ad510f0d41c2
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50128873"
---
# <a name="tutorial-azure-active-directory-integration-with-appinux"></a>Tutoriel : Intégration d’Azure Active Directory à Appinux

Dans ce tutoriel, vous allez apprendre à intégrer Appinux dans Azure Active Directory (Azure AD).

L’intégration d’Appinux dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Appinux.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Appinux (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Appinux, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Appinux pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Appinux depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-appinux-from-the-gallery"></a>Ajout d’Appinux depuis la galerie

Pour configurer l’intégration d’Appinux à Azure AD, vous devez ajouter Appinux, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Appinux depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Appinux**, sélectionnez **Appinux** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Appinux dans la liste des résultats](./media/appinux-tutorial/tutorial_appinux_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Appinux avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Appinux équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Appinux associé doit être établie.

Pour configurer et tester l'authentification unique Azure AD avec Appinux, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Appinux](#creating-an-appinux-test-user)** pour avoir dans Appinux un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Appinux.

**Pour configurer l’authentification unique Azure AD avec Appinux, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Appinux**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](./media/appinux-tutorial/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](./media/appinux-tutorial/tutorial_appinux_editurl.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Appinux](./media/appinux-tutorial/tutorial_appinux_url1.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<Appinux_SUBDOMAIN>.appinux.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<Appinux_SUBDOMAIN>.appinux.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support client Appinux](https://support.appinux.com).

5. L’application Appinux attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Section de l’attribut](./media/appinux-tutorial/edit_attribute.png)

    ![Section de l’attribut](./media/appinux-tutorial/tutorial_namespace.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | **Nom** | **Espace de noms** | **Attribut source**|
    | ---------|---------------| --------- |
    | `givenname` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.givenname` |
    | `surname` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.surname` |
    | `emailaddress` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.mail` |
    | `name` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.userprincipalname` |
    | `UserType` | `http://bcv.appinux.com/claims` | `Provide the value as per your organization` |
    | `Tag` | `http://appinux.com/Tag` | `Provide the value as per your organization` |
    | `Role` | `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` | `user.assignedroles` |
    | `email` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/email` | `user.mail` |
    | `wanshort` | `http://appinux.com/windowsaccountname2` | `extractmailprefix([userprincipalname])` |
    | `nameidentifier` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` | `user.employeeid` |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Nouvel attribut](./media/appinux-tutorial/new_save_attribute.png)

    ![Ajouter un attribut](./media/appinux-tutorial/new_attribute_details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la zone de texte **Espace de noms**, indiquez la valeur d’espace de noms pour cette ligne.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/appinux-tutorial/tutorial_appinux_certificate.png) 

8. Pour configurer l’authentification unique côté **Appinux**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à l’[équipe du support technique Appinux](https://support.appinux.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](./media/appinux-tutorial/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](./media/appinux-tutorial/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-an-appinux-test-user"></a>Création d’un utilisateur de test Appinux

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Appinux. Appinux prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un nouvel utilisateur est créé lors d’une tentative d’accès à Appinux.
>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’ [équipe du support technique Appinux](https://support.appinux.com).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Appinux.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Appinux**.

    ![Configurer l'authentification unique](./media/appinux-tutorial/tutorial_appinux_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Appinux dans le panneau d’accès, vous devez être connecté automatiquement à votre application Appinux.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appinux-tutorial/tutorial_general_01.png
[2]: ./media/appinux-tutorial/tutorial_general_02.png
[3]: ./media/appinux-tutorial/tutorial_general_03.png
[4]: ./media/appinux-tutorial/tutorial_general_04.png

[100]: ./media/appinux-tutorial/tutorial_general_100.png

[200]: ./media/appinux-tutorial/tutorial_general_200.png
[201]: ./media/appinux-tutorial/tutorial_general_201.png
[202]: ./media/appinux-tutorial/tutorial_general_202.png
[203]: ./media/appinux-tutorial/tutorial_general_203.png
