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
ms.date: 09/10/2018
ms.author: jeedes
ms.openlocfilehash: c611fd7893a96113a4a9f2454bcd0b11db02be29
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605100"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Tutoriel : Intégration d’Azure Active Directory avec Snowflake

Ce tutoriel vous montre comment intégrer Snowflake avec Azure Active Directory (Azure AD).

L’intégration de Snowflake avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Snowflake.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Snowflake (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Snowflake, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement avec authentification unique à Snowflake
- Les clients qui n’ont pas de compte Snowflake et qui souhaitent l’essayer via la galerie d’applications Azure AD peuvent consulter [ce](https://trial.snowflake.net/?cloud=azure&utm_source=azure-marketplace&utm_medium=referral&utm_campaign=self-service-azure-mp) lien.

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

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Snowflake](#create-a-snowflake-test-user)** pour avoir un équivalent de Britta Simon dans Snowflake lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Snowflake.

**Pour configurer l’authentification unique Azure AD avec Snowflake, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Snowflake**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/snowflake-tutorial/tutorial_snowflake_samlbase.png)

3. Dans la section **Domaine et URL Snowflake**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/snowflake-tutorial/tutorial_general_400.png)
    
7. Dans la section **Configuration de Snowflake**, cliquez sur **Configurer Snowflake** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Snowflake](./media/snowflake-tutorial/tutorial_snowflake_configure.png) 

8. Ouvrez une autre fenêtre de navigateur web et connectez-vous à Snowflake en tant qu’administrateur de la sécurité.

9. Exécutez la requête SQL ci-dessous sur la feuille de calcul en définissant la valeur du **certificat** sur la valeur du **certificat téléchargé** et la valeur **ssoUrl** sur **l’URL du service SSO SAML** d’Azure AD avec la valeur indiquée ci-dessous.

    ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png) 

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<SAML single sign-on service URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/snowflake-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/snowflake-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/snowflake-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/snowflake-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-snowflake-test-user"></a>Créer un utilisateur de test Snowflake

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

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Snowflake.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Snowflake, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Snowflake**.

    ![Lien Snowflake dans la liste des applications](./media/snowflake-tutorial/tutorial_snowflake_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Snowflake dans le volet d’accès, vous devez être connecté automatiquement à votre application Snowflake.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/snowflake-tutorial/tutorial_general_01.png
[2]: ./media/snowflake-tutorial/tutorial_general_02.png
[3]: ./media/snowflake-tutorial/tutorial_general_03.png
[4]: ./media/snowflake-tutorial/tutorial_general_04.png

[100]: ./media/snowflake-tutorial/tutorial_general_100.png

[200]: ./media/snowflake-tutorial/tutorial_general_200.png
[201]: ./media/snowflake-tutorial/tutorial_general_201.png
[202]: ./media/snowflake-tutorial/tutorial_general_202.png
[203]: ./media/snowflake-tutorial/tutorial_general_203.png

