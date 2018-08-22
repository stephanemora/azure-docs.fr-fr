---
title: 'Didacticiel : Intégration d’Azure Active Directory à Cisco Webex | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005515"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Didacticiel : Intégration d’Azure Active Directory à Cisco Webex

Ce didacticiel explique comment intégrer Cisco Webex avec Azure Active Directory (Azure AD).

L’intégration de Cisco Webex avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Cisco Webex.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Cisco Webex avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour plus d’informations sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Cisco Webex, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Cisco Webex pour lequel l’authentification unique est activée

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez [obtenir un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Cisco Webex à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-cisco-webex-from-the-gallery"></a>Ajouter Cisco Webex à partir de la galerie
Pour configurer l’intégration de Cisco Webex à Azure AD, vous devez ajouter Cisco Webex, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Cisco Webex à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Cisco Webex**. 

5. Sélectionnez **Cisco Webex** dans le volet de résultats. Sélectionnez ensuite le bouton **Ajouter** pour ajouter l’application.

    ![Cisco Webex dans la liste des résultats](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Cisco Webex sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Cisco Webex équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Cisco Webex associé.

Dans Cisco Webex, donnez à la valeur **Username** la même valeur que **Nom d’utilisateur** dans Azure AD. Vous avez maintenant établi le lien entre les deux utilisateurs. 

Pour configurer et tester l’authentification unique Azure AD avec Cisco Webex, suivez les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Cisco Webex](#create-a-cisco-webex-test-user) pour avoir un équivalent de Britta Simon dans Cisco Webex, lié à la représentation Azure AD associée.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Cisco Webex.

**Pour configurer l’authentification unique Azure AD avec Cisco Webex, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Cisco Webex**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, dans la zone de liste déroulante **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Cisco Webex en tant qu’administrateur.

4. Cliquez sur **Settings** (Paramètres) à gauche du menu.

    ![Configurer l'authentification unique](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. Faites défiler la page Paramètres jusqu’à la section **Authentication** (Authentification), puis cliquez sur **Modify** (Modifier).

    ![Configurer l'authentification unique](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Sélectionnez **intégrer un fournisseur d’identité tiers. (Avancé)** et accédez à l’écran suivant.

    ![Configurer l'authentification unique](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Dans la page **Export Directory Metadata** (Exporter les métadonnées de répertoire), cliquez sur **Download Metadata File** (Télécharger le fichier de métadonnées).

    ![Configurer l'authentification unique](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. Dans le portail Azure, sous la section**Cisco Webex Domain and URLs** (Domaine et URL Cisco Webex), chargez le **fichier de métadonnées du fournisseur de services** téléchargé et effectuez les étapes suivantes pout configurer l’application :

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Informations d’authentification unique dans Domaine et URL Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![Informations d’authentification unique dans Domaine et URL Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. Une fois le chargement du **fichier de métadonnées du fournisseur de services** terminé, les valeurs **Identifier** (Identificateur) et **Reply URL** (URL de réponse) sont automatiquement remplies dans la zone de texte de la section **Cisco Webex Domain and URLs** (Domaine et URL Cisco Webex), comme indiqué ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.webex.com/`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez [l’équipe du support client Cisco Webex](https://www.webex.co.in/support/support-overview.html).

9. L’application Cisco Webex s’attend à ce que les assertions SAML contiennent des attributs spécifiques. Configurez les attributs suivants pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    |  Nom de l'attribut  | Valeur de l’attribut |
    | --------------- | -------------------- |    
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   uid    | user.mail |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

11. Dans la section **Certificat de signature SAML**, sélectionnez **Métadonnées XML**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. Dans la page administrateur du site d’entreprise Cisco Webex, utilisez l’option de navigateur de fichiers pour localiser et charger le fichier de métadonnées Azure AD. Ensuite, sélectionnez **Require certificate signed by a certificate authority in Metadata (more secure)** (Exiger un certificat signé par une autorité de certification dans les métadonnées [plus sécurisé]), puis passez à l’écran suivant. 

    ![Configurer l'authentification unique](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. Sélectionnez **Test SSO Connection** (Tester la connexion SSO), puis, quand un nouvel onglet de navigateur s’ouvre, et authentifiez-vous auprès d’Azure AD en vous connectant.

15. Revenez à l’onglet du navigateur **Cisco Cloud Collaboration Management** (Gestion de la collaboration dans le cloud Cisco). Si le test a réussi, sélectionnez **Ce test a réussi. Activez l’option Authentification unique**, puis cliquez sur **Save** (Enregistrer).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-cisco-webex-test-user"></a>Créer un utilisateur de test Cisco Webex

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Cisco Webex. Cisco Webex prend en charge le provisionnement juste-à-temps et le provisionnement d’utilisateurs automatique, qui est activé par défaut. Vous trouverez plus d’informations [ici](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial) sur la façon de configurer l’attribution automatique d’utilisateurs.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cisco Webex.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Cisco Webex, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications. Ensuite, accédez à la vue d’annuaire, puis à **Applications d’entreprise**.  

2. Sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

3. Dans la liste des applications, sélectionnez **Cisco Webex**.

    ![Lien Cisco Webex dans la liste des applications](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Cliquez sur le bouton **Attribuer** dans la boîte de dialogue **Ajouter une attribution**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette Cisco Webex dans le volet d’accès, vous êtes connecté automatiquement à votre application Cisco Webex.

Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

