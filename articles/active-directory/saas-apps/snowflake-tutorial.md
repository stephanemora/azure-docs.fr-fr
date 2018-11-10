---
title: 'Tutoriel : Intégration d’Azure Active Directory avec Snowflake | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Snowflake.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092557"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Tutoriel : Intégration d’Azure Active Directory avec Snowflake

Ce tutoriel vous montre comment intégrer Snowflake avec Azure Active Directory (Azure AD).

L’intégration de Snowflake avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Snowflake.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Snowflake (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Snowflake, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement avec authentification unique à Snowflake

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Snowflake depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-snowflake-from-the-gallery"></a>Ajout de Snowflake depuis la galerie

Pour configurer l’intégration de Snowflake dans Azure AD, vous devez ajouter Snowflake à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Snowflake depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Snowflake**, sélectionnez **Snowflake** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Snowflake dans la liste des résultats](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Snowflake avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans Snowflake équivaut à un utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Snowflake associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Snowflake, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Snowflake](#creating-snowflake-test-user)** pour avoir un équivalent de Britta Simon dans Snowflake lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Snowflake.

**Pour configurer l’authentification unique Azure AD avec Snowflake, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Snowflake**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels.

6. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7. Dans la section **Configurer Snowflake**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de Snowflake](common/configuresection.png)

8. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Snowflake en tant qu’administrateur de la sécurité.

9. **Sélectionnez le rôle** **ACCOUNTADMIN** en cliquant sur **profil** en haut à droite de la page.

    > [!NOTE]
    > Ce n’est pas dans le même contexte que vous avez sélectionné en haut à droite, sous votre nom d’utilisateur
    
    ![L’administrateur Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. Ouvrez le **certificat Base 64 téléchargé** dans le Bloc-notes. Copiez la valeur entre « -----BEGIN CERTIFICATE----- » et « -----END CERTIFICATE----- », et collez-la entre les guillemets situés à côté de **certificate** ci-dessous. Dans **ssoUrl**, collez la valeur de l’**URL de connexion** que vous avez copiée sur le portail Azure. Sélectionnez **Toutes les requêtes** et cliquez sur **Exécuter**.

    ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

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

### <a name="creating-snowflake-test-user"></a>Création d’un utilisateur de test Snowflake

Pour permettre aux utilisateurs Azure AD de se connecter à Snowflake, vous devez les approvisionner dans Snowflake. Dans Snowflake, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à Snowflake en tant qu’administrateur de la sécurité.

2. **Sélectionnez le rôle** **ACCOUNTADMIN** en cliquant sur **profil** en haut à droite de la page.  

    ![L’administrateur Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Créez l’utilisateur en exécutant la requête SQL ci-dessous. Vérifiez que la valeur de « LOGIN_NAME » correspond au nom d’utilisateur Azure AD sur la feuille de calcul, comme indiqué ci-dessous.

    ![L’adminsql Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Snowflake.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Snowflake**.

    ![Configurer l'authentification unique](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Snowflake dans le volet d’accès, vous devez être connecté automatiquement à votre application Snowflake.
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
