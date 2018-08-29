---
title: 'Didacticiel : intégration d’Azure Active Directory à Salesforce Sandbox | Microsoft Docs'
description: Découvrez comment configurer une authentification unique entre Azure Active Directory et Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 6feafba41cf65a752dd5bf0819b0b93bacff0aff
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42140849"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Didacticiel : Intégration d’Azure Active Directory à Salesforce Sandbox

Dans ce didacticiel, vous allez apprendre à intégrer Salesforce Sandbox avec Azure Active Directory (Azure AD).

Les bacs à sable (sandbox) vous permettent de créer plusieurs copies de votre organisation dans des environnements distincts à des fins diverses, notamment le développement, le test et la formation, sans compromettre les données ou les applications de votre organisation de production Salesforce.
Pour plus d’informations, consultez la page de [présentation Salesforce des bacs à sable](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

L’intégration de Salesforce Sandbox avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Salesforce Sandbox.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Salesforce Sandbox (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Salesforce Sandbox, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Salesforce Sandbox pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Salesforce Sandbox à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Ajout de Salesforce Sandbox à partir de la galerie

Pour configurer l’intégration de Salesforce Sandbox avec Azure AD, vous devez ajouter Salesforce Sandbox à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Salesforce Sandbox à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Salesforce Sandbox**, sélectionnez **Salesforce Sandbox** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Salesforce Sandbox dans la liste des résultats](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Salesforce Sandbox sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Salesforce Sandbox équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Salesforce Sandbox associé doit être établie.

Dans Salesforce Sandbox, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec Salesforce Sandbox, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)** pour avoir un équivalent de Britta Simon dans Salesforce Sandbox lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Salesforce Sandbox.

**Pour configurer l’authentification unique Azure AD avec Salesforce Sandbox, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Salesforce Sandbox**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Dans la section **Domaine et URL Salesforce Sandbox**, exécutez la procédure ci-après si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité** :

   ![Informations d’authentification unique dans Domaine et URL Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

   Dans la zone de texte **URL de réponse**, tapez **l’URL de réponse** de votre organisation.

   > [!NOTE]
   > Mettez à jour la valeur de l’URL de réponse avec l’URL de réponse réelle. La procédure à suivre est expliquée plus loin dans ce didacticiel.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Salesforce Sandbox**, cliquez sur **Configurer Salesforce Sandbox** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

7. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce Sandbox.

8. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure1.png)

9. Dans le volet de navigation de gauche, accédez à **SETTINGS** (PARAMÈTRES), puis cliquez sur **Identity** (Identité) pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure3.png)

11. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Dans la section **Single Sign-On Settings** (Paramètres de l’authentification unique), procédez comme suit :

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-saml-config1.png)

    a. Cochez la case **SAML Enabled** (Compatible SAML).

    b. Dans le champ **Issuer**, collez la valeur de **ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    c. Pour charger le **Identity Provider Certificate** (Certificat du fournisseur d’identité), cliquez sur **Browse** (Parcourir) afin de sélectionner le fichier de certificat que vous avez téléchargé à partir du Portail Azure.

    d. Dans la zone de texte **Identity Provider Login URL**, collez la valeur de l’**URL du service d’authentification unique** que vous avez copiée à partir du portail Azure.

    e. Comme **SAML Identity Type**, choisissez l’une des options suivantes :

      * Sélectionnez **Assertion contains the User’s Salesforce username** si le Salesforce Username de l’utilisateur est passé dans l’assertion SAML.

      * Sélectionnez **Assertion contains the Federation ID from the User object** si le Federation ID de l’objet User est passé dans l’assertion SAML.
  
    f. Dans le champ **SAML Identity Location** (Emplacement d’identité SAML), sélectionnez **Identity is in an Attribute element** (L’identité se trouve dans un élément Attribute).

    g. SFDC ne prend pas en charge la déconnexion SAML.  Pour contourner ce problème, collez `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` dans la zone de texte **Custom Logout URL** (URL de connexion personnalisée).

    h. Cliquez sur **Enregistrer**.

14. Dans la page **Single Sign-On Settings** (Paramètres de l’authentification unique), cliquez sur le bouton **Download Metadata** (Télécharger les métadonnées).

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure4.png)

15. Ouvrez les métadonnées téléchargées dans une autre fenêtre de navigateur, copiez la valeur de l’élément **Location** et collez-la dans la zone de texte **URL de réponse** dans la section **Domaine et URL Salesforce Sandbox** du Portail Azure.  

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure5.png)

16. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, vous devez respecter les prérequis suivants :

    a. Vous devez disposer d’un domaine vérifié.

    b. Vous devez configurer et activer votre domaine sur Salesforce Sandbox. La procédure à suivre est décrite dans la suite de ce didacticiel.

    c. Dans le Portail Azure, au niveau de la section **Domaine et URL Salesforce Sandbox**, cochez la case **Afficher les paramètres d’URL avancés** et procédez comme suit :
  
    ![Informations d’authentification unique dans Domaine et URL Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Cette valeur doit être copiée à partir du portail Salesforce Sandbox une fois que vous avez activé le domaine.

17. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

19. Dans la section **Configuration de Salesforce Sandbox**, cliquez sur **Configurer Salesforce Sandbox** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

20. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce Sandbox.

21. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure1.png)

22. Dans le volet de navigation de gauche, accédez à **SETTINGS** (PARAMÈTRES), puis cliquez sur **Identity** (Identité) pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

23. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure3.png)

24. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

25. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

26. Si vous ajoutez une seconde instance, vous devez activer un domaine comme décrit ci-dessus (pour le mode initié par le fournisseur de service). Dans la section SAML Single Sign-On Settings, procédez comme suit :

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. Dans la zone de texte **Name**, tapez le nom de la configuration (par exemple, *SPSSOWAAD_Test*).

    b. Dans le champ **Issuer**, collez la valeur de **ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Entity ID** (ID d’entité), utilisez la valeur `https://test.salesforce.com` pour la première instance et, à partir de la seconde instance de l’application, vous pouvez utiliser la valeur de l’identificateur propre au locataire.

    d. Dans **Identity Provider Certificate**, cliquez sur **Choose File** pour sélectionner le fichier de certificat que vous avez téléchargé à partir du portail Azure.

    e. Comme **SAML Identity Type**, choisissez l’une des options suivantes :

      * Sélectionnez **Assertion contains the User’s Salesforce username** si le Salesforce Username de l’utilisateur est passé dans l’assertion SAML.

      * Sélectionnez **Assertion contains the Federation ID from the User object** si le Federation ID de l’objet User est passé dans l’assertion SAML.

      * Sélectionnez **Assertion contains the Use ID from the User object** si le Federation ID de l’objet User est passé dans l’assertion SAML.

    f. Pour **SAML Identity Location (Emplacement de l’identité SAML)**, sélectionnez **Identity is in the NameIdentifier element of the Subject statement (L’identité figure dans l’élément NameIdentifier de l’instruction Subject)**.

    g. Pour **Service Provider Initiated Request Binding (Liaison de demande initiée par le fournisseur de services)**, sélectionnez **HTTP POST**.

    h. Dans la zone de texte **Identity Provider Login URL**, collez la valeur de l’**URL du service d’authentification unique** que vous avez copiée à partir du portail Azure.

    i. SFDC ne prend pas en charge la déconnexion SAML.  Pour contourner ce problème, collez `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` dans la zone de texte **Custom Logout URL** (URL de connexion personnalisée).

    j. Cliquez sur **Enregistrer**.

27. Pour activer votre domaine sur Salesforce Sandbox, procédez comme suit :

    > [!NOTE]
    > Avant d’activer le domaine, vous devez créer le même sur Salesforce Sandbox. Pour plus d’informations, voir [Définition de votre nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Une fois le domaine créé, assurez-vous qu’il est configuré correctement.

    * Dans le volet de navigation de gauche de Salesforce Sandbox, cliquez sur **Company Settings** (Paramètres de l’entreprise) pour développer la section associée, puis cliquez sur **My Domain** (Mon domaine).

         ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * Dans la section **Authentication Configuration** (Configuration de l’authentification), cliquez sur **Authentication Policy** (Stratégie d’authentification).

        ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * Dans la section **Authentication Configuration** (Configuration de l’authentification), pour le champ **Authentication Service** (Service d’authentification), sélectionnez le nom du paramètre d’authentification unique SAML que vous avez défini lors de la configuration SSO dans Salesforce Sandbox, puis cliquez sur **Save** (Enregistrer).

        ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Créer un utilisateur de test Salesforce Sandbox

Dans cette section, un utilisateur nommé Britta Simon est créé dans Salesforce Sandbox. Salesforce Sandbox prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Salesforce Sandbox, un nouveau est créé lorsque vous tentez d’accéder à Salesforce Sandbox. Salesforce Sandbox prend également en charge l’attribution automatique d’utilisateurs, vous trouverez [ici](salesforce-sandbox-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Salesforce Sandbox.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Salesforce Sandbox, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Salesforce Sandbox**.

    ![Lien Salesforce Sandbox dans la liste des applications.](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Salesforce Sandbox dans le panneau d’accès, vous devez être connecté automatiquement à votre application Salesforce Sandbox.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement de l’utilisateur](salesforce-sandbox-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png