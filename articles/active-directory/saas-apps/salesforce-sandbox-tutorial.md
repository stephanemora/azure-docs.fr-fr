---
title: "Didacticiel : Intégration d'Azure Active Directory à Salesforce Sandbox | Microsoft Docs"
description: Découvrez comment configurer une authentification unique entre Azure Active Directory et Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: 33d39e617bd55b88d8f04785d937faf176463ee5
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808814"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutoriel : Intégration d'Azure Active Directory à Salesforce Sandbox

Dans ce didacticiel, vous allez apprendre à intégrer Salesforce Sandbox avec Azure Active Directory (Azure AD).

Les bacs à sable (sandbox) vous permettent de créer plusieurs copies de votre organisation dans des environnements distincts à des fins diverses, notamment le développement, le test et la formation, sans compromettre les données ou les applications de votre organisation de production Salesforce.
Pour plus d'informations, consultez  [Présentation de Sandbox](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

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

2. Cliquez sur **Modifier le mode d’authentification unique** au-dessus de l’écran pour sélectionner le mode **SAML**.

      ![Lien Configurer l’authentification unique](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Lien Configurer l’authentification unique](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.
   
    ![Lien Configurer l’authentification unique](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode démarré par **IDP** :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Charger le fichier de métadonnées](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![choisir le fichier de métadonnées](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Vous obtenez le fichier de métadonnées du fournisseur de services à partir du portail d’administration Salesforce Sandbox, décrit plus loin dans le tutoriel.

    c. Une fois que le fichier de métadonnées est correctement chargé, la valeur **URL de réponse** est renseignée automatiquement dans la zone de texte **URL de réponse**.

    ![Informations d’authentification unique dans Domaine et URL Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger **XML de métadonnées de fédération**, puis enregistrez le fichier XML sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce Sandbox.

8. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure1.png)

9. Dans le volet de navigation de gauche, accédez à **SETTINGS** (PARAMÈTRES), puis cliquez sur **Identity** (Identité) pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure3.png)

11. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau à partir d’un fichier de métadonnées**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Cliquez sur **Sélectionner le fichier** pour charger le fichier XML de métadonnées que vous avez téléchargé à partir du Portail Azure, puis cliquez sur **Créer**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Sur la page **Paramètres d’authentification unique SAML**, les champs se renseignent automatiquement et cliquez sur Enregistrer.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. Dans la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Télécharger les métadonnées** pour télécharger le fichier de métadonnées du fournisseur de services. Utilisez ce fichier dans la section **Configuration SAML de base** dans le portail Azure pour configurer les URL nécessaires comme expliqué ci-dessus.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure4.png)

16. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, vous devez respecter les prérequis suivants :

    a. Vous devez disposer d’un domaine vérifié.

    b. Vous devez configurer et activer votre domaine sur Salesforce Sandbox. La procédure à suivre est décrite dans la suite de ce didacticiel.

    c. Dans le portail Azure, dans la section **Configuration SAML de base**, cliquez sur **Définir des URL supplémentaires** et effectuez l’étape suivante :
  
    ![Informations d’authentification unique dans Domaine et URL Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Cette valeur doit être copiée à partir du portail Salesforce Sandbox une fois que vous avez activé le domaine.

17. Dans la section **Certificat de signature SAML**, cliquez sur **XML de métadonnées de fédération**, puis enregistrez le fichier xml sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce Sandbox.

19. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure1.png)

20. Dans le volet de navigation de gauche, accédez à **SETTINGS** (PARAMÈTRES), puis cliquez sur **Identity** (Identité) pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/configure3.png)

22. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau à partir d’un fichier de métadonnées**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Cliquez sur **Choisir un fichier** pour charger le fichier XML de métadonnées, puis sur **Créer**.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Sur la page **Paramètres d'authentification unique SAML**, les champs sont automatiquement renseignés. Entrez le nom de la configuration (par exemple : *SPSSOWAAD_Test*) dans la zone de texte **Nom** et cliquez sur Enregistrer.

    ![Configurer l'authentification unique](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Pour activer votre domaine sur Salesforce Sandbox, procédez comme suit :

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

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

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

4. Cliquez sur le bouton **Add User** (Ajouter un utilisateur). Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

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