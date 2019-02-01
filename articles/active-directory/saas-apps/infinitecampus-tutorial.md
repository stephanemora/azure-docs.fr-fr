---
title: 'Tutoriel : Intégration d’Azure Active Directory à Infinite Campus | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 303d794e558a5e85b4dd2bca3d9bbb4c2a1ff5dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151802"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Tutoriel : Intégration d’Azure Active Directory à Infinite Campus

Ce didacticiel explique comment intégrer Infinite Campus avec Azure Active Directory (Azure AD).

L’intégration d’Infinite Campus avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Infinite Campus.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Infinite Campus (via une authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Infinite Campus, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Infinite Campus pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).
- Au minimum, vous devez être administrateur Azure Active Directory pour terminer la configuration.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Infinite Campus à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-infinite-campus-from-the-gallery"></a>Ajout d’Infinite Campus à partir de la galerie

Pour configurer l’intégration d’Infinite Campus avec Azure AD, vous devez ajouter Infinite Campus à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Infinite Campus à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Infinite Campus**, sélectionnez **Infinite Campus** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Infinite Campus dans la liste des résultats](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Infinite Campus à l’aide d’un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur d’Infinite Campus équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Infinite Campus associé.

Pour configurer et tester l’authentification unique Azure AD avec Infinite Campus, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Infinite Campus](#creating-a-infinite-campus-test-user)** pour obtenir un équivalent de Britta Simon dans Infinite Campus, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Infinite Campus.

**Pour configurer l’authentification unique Azure AD avec Infinite Campus, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Infinite Campus**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

5. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services** (passez à l’étape **11.c**), effectuez les étapes suivantes :

    a. Cliquez sur **Charger un fichier de métadonnées**.

        ![image](common/b9_saml.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![image](common/b9(1)_saml.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la zone de texte de la section **Configuration SAML de base**, comme indiqué ci-dessous :

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. Dans la zone de texte **URL de connexion**, entrez une URL au format suivant (le domaine varie en fonction du modèle d’hébergement) : `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > Sur la page de configuration du fournisseur du service d’authentification unique de Infinite Campus, vous obtenez le **fichier de métadonnées du fournisseur de services**, abordé plus loin dans le didacticiel. Si vous débutez avec une nouvelle configuration de fournisseur de services SAML dans Infinite Campus, passez à l’**étape 11** pour terminer l’exportation du fichier de métadonnées du fournisseur de services.

6. Si vous n’avez pas de **fichier de métadonnées du fournisseur de services**, effectuez les étapes suivantes (notez que le domaine varie selon le modèle d’hébergement) :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Informations sur l’authentification unique de Domaine et d’URL Infinite Campus](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. Sur la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur l’**icône** de copie pour copier l’ **URL des métadonnées de fédération de l’application** , puis collez-la dans le bloc-notes.

    ![Lien Téléchargement de certificat](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Dans la section **Configurer Infinite Campus**, utilisez les valeurs suivantes pour valider le chargement ou l’utilisation du fichier de métadonnées ou de l’URL Azure.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration d’Infinite Campus](common/configuresection.png)

8. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Infinite Campus en tant qu’administrateur de la sécurité.

9. Sur le côté gauche du menu, cliquez sur **Administration de système**.

    ![Administrateur](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Accédez à **Sécurité utilisateur** > **Gestion SAML** > **Configuration du fournisseur de services d’authentification unique**.

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. Dans la page **Configuration du fournisseur de services d’authentification unique**, procédez comme suit :

    ![Authentification unique](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Sélectionnez **Activer l’authentification unique SAML**.
    
    b. Dans la section **Select an option to retrieve Identity Provider (IDP) server data** (Sélectionnez une option pour récupérer les données de serveur du fournisseur d’identité), sélectionnez **Metadata URL** (URL des métadonnées), collez l’**URL des métadonnées de fédération d’application** dans la zone, puis cliquez sur **Sync** (Synchroniser).

    c. Cliquez sur le lien **Service Provider Metadata** (Métadonnées du fournisseur de services) pour enregistrer le **fichier de métadonnées du fournisseur de services** sur votre ordinateur, et le charger dans la section **Configuration SAML de base** afin de renseigner automatiquement les valeurs **Identificateur** et **URL de réponse** du portail Azure (reportez-vous à l’étape 4 pour charger et remplir automatiquement les valeurs, ou à l’étape 5 pour les entrer manuellement).

    d. Après avoir cliqué sur **Synchroniser**, les valeurs sont automatiquement renseignées dans la page **Configuration du fournisseur de services d’authentification unique**.

    e. Cliquez sur **Enregistrer**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test _unique_ appelé Britta Simon dans le portail Azure.

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

### <a name="creating-a-infinite-campus-test-user"></a>Création d’un utilisateur de test Infinite Campus

L’architecture d’Infinite Campus est centrée sur des données démographiques. Pour ajouter des utilisateurs dans la plateforme Infinite Campus, veuillez contacter l’[équipe de support technique d’Infinite Campus](mailto:sales@infinitecampus.com).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Infinite Campus.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Infinite Campus**.

    ![Configurer l'authentification unique](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Infinite Campus dans le volet d’accès, vous devez être connecté automatiquement à votre application Infinite Campus. Si vous vous connectez à l’application Infinite Campus dans le même navigateur que celui où vous administrez Azure AD, veillez à vous connecter à Azure AD en tant qu’utilisateur de test. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

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
