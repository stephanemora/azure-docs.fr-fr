---
title: 'Tutoriel : Intégration d’Azure Active Directory à Peakon | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 637988179228fbf0a6000de74a1185af98277e3c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178951"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Tutoriel : Intégration d’Azure Active Directory à Peakon

Dans ce tutoriel, vous allez apprendre à intégrer Peakon à Azure Active Directory (Azure AD).

L’intégration de Peakon à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Peakon.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Peakon (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Peakon, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Peakon pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas un environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Peakon à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-peakon-from-the-gallery"></a>Ajout de Peakon à partir de la galerie

Pour configurer l’intégration de Peakon à Azure AD, vous devez ajouter Peakon, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Peakon à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Peakon**, sélectionnez **Peakon** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Peakon dans la liste des résultats](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Peakon à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Peakon équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Peakon associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Peakon, vous devez effectuer les modules suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Peakon](#creating-a-peakon-test-user)** pour avoir un équivalent de Britta Simon dans Peakon lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Peakon.

**Pour configurer l’authentification unique Azure AD avec Peakon, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Peakon**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL de Peakon](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://app.peakon.com/saml/<companyid>/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://app.peakon.com/saml/<companyid>/assert`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL de Peakon](./media/peakon-tutorial/tutorial_peakon_url1.png)

    Dans la zone de texte **URL d’authentification**, tapez l’URL `https://app.peakon.com/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. La procédure est expliquée plus loin dans le tutoriel.

6. Dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. Dans la section **Configurer Peakon**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de Peakon](common/configuresection.png)

8. Dans une autre fenêtre de navigateur web, connectez-vous à Peakon en tant qu’administrateur.

9. Dans la barre de menus située à gauche de la page, cliquez sur **Configuration**, puis accédez à **Integrations** (Intégrations).

    ![Configuration](./media/peakon-tutorial/tutorial_peakon_config.png)

10. Dans la page **Integrations** (Intégrations), cliquez sur **Single Sign-On** (Authentification unique).

    ![Authentification unique](./media/peakon-tutorial/tutorial_peakon_single.png)

11. Sous la section **Single Sign-On** (Authentification unique), cliquez sur **Enable** (Activer).

    ![Activation](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. Dans la section **Single sign-on for employees using SAML** (Authentification unique pour les employés avec SAML), effectuez les étapes suivantes :

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Dans la zone de texte **SSO Login URL** (URL de connexion avec authentification unique), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    b. Dans la zone de texte **SSO Logout URL** (URL de déconnexion avec authentification unique), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Cliquez sur **Choose a File** (Choisir un fichier) pour charger le certificat que vous avez téléchargé à partir du portail Azure dans la zone Certificate (Certificat).

    d. Cliquez sur **l’icône** pour copier **Entity ID** (ID d’entité) et le coller dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    e. Cliquez sur **l’icône** pour copier **Reply URL (ACS)** (URL de réponse [ACS]) et la coller dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    f. Cliquez sur **Enregistrer**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-a-peakon-test-user"></a>Création d’un utilisateur de test Peakon

Pour se connecter à Peakon, les utilisateurs Azure AD doivent être provisionnés dans Peakon.  
Dans le cas de Peakon, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Peakon en tant qu’administrateur.

2. Dans la barre de menus située à gauche de la page, cliquez sur **Configuration**, puis accédez à  **Employees** (Employés).

    ![Employé](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. En haut à droite de la page, cliquez sur  **Add employee** (Ajouter un employé).

      ![Ajouter un employé](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Dans la boîte de dialogue **New employee** (Nouvel employé), effectuez les étapes suivantes :

     ![Nouvel employé](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Dans la zone de texte **Name** (Nom), entrez **Britta** comme prénom et **simon** comme nom.

    b. Dans la zone de texte **Email** (E-mail), tapez une adresse e-mail. Par exemple : **Brittasimon@contoso.com**.

    c. Cliquez sur **Créer un employé**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Peakon.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Peakon**.

    ![Configurer l'authentification unique](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Peakon dans le volet d’accès, vous devez être connecté automatiquement à votre application Peakon.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
