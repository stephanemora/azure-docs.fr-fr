---
title: 'Tutoriel : Intégration d’Azure Active Directory à SpringCM | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.openlocfilehash: 7c42ff6a78e66e602abb06dc40a413f27ec7ea52
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820289"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutoriel : Intégration d’Azure Active Directory à SpringCM

Dans ce didacticiel, vous allez apprendre à intégrer SpringCM à Azure Active Directory (Azure AD).

L’intégration de SpringCM dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SpringCM
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SpringCM (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SpringCM, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SpringCM pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SpringCM à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-springcm-from-the-gallery"></a>Ajout de SpringCM à partir de la galerie
Pour configurer l’intégration de SpringCM avec Azure AD, vous devez ajouter SpringCM, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SpringCM à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **SpringCM**.

    ![Création d’un utilisateur de test Azure AD](./media/spring-cm-tutorial/tutorial_springcm_search.png)

1. Dans le volet de résultats, sélectionnez **SpringCM**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SpringCM avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SpringCM équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur SpringCM associé doit être établie.

Dans SpringCM, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SpringCM, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test SpringCM](#creating-a-springcm-test-user)** pour avoir un équivalent de Britta Simon dans SpringCM lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SpringCM.

**Pour configurer l’authentification unique Azure AD avec SpringCM, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SpringCM**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/spring-cm-tutorial/tutorial_springcm_samlbase.png)

1. Dans la section **Domaine et URL SpringCM**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/spring-cm-tutorial/tutorial_springcm_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Pour obtenir cette valeur, contactez l’[équipe du support client SpringCM](https://knowledge.springcm.com/support). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l'authentification unique](./media/spring-cm-tutorial/tutorial_springcm_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/spring-cm-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de SpringCM**, cliquez sur **Configurer SpringCM** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configurer l'authentification unique](./media/spring-cm-tutorial/tutorial_springcm_configure.png)     

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise **SpringCM** en tant qu’administrateur.

1. Dans le menu en haut, cliquez sur **GO TO**, sur **Preferences** puis, dans la section **Account Preferences**, cliquez sur **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Dans la section Identity Provider Configuration, procédez comme suit :
   
    ![Configuration du fournisseur d’identité](./media/spring-cm-tutorial/ic797052.png "Configuration du fournisseur d’identité")
    
    a. Pour charger votre certificat Azure Active Directory téléchargé, cliquez sur **Select Issuer Certificate** ou **Change Issuer Certificate**.
    
    b. Dans la zone de texte **Issuer**, collez la valeur **ID d’entité SAML** que vous avez copiée à partir du portail Azure.
    
    c. Collez l’**URL du service d’authentification unique SAML** que vous avez copiée dans le portail Azure dans la zone de texte **Service Provider (SP) Initiated Endpoint**.
            
    d. Sélectionnez **Enable** pour **SAML Enabled**.

    e. Cliquez sur **Enregistrer**.
 
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/spring-cm-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/spring-cm-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/spring-cm-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/spring-cm-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-springcm-test-user"></a>Création d’un utilisateur de test SpringCM

Pour se connecter à SpringCM, les utilisateurs d’Azure AD doivent être approvisionnés dans SpringCM. Dans le cas de SpringCM, l’approvisionnement est une tâche manuelle.

>[!NOTE]
>Pour plus d’informations, consultez [Créer et modifier un utilisateur SpringCM](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Pour approvisionner un compte d’utilisateur dans SpringCM, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **SpringCM** en tant qu’administrateur.

1. Cliquez sur **GOTO** puis sur **ADDRESS BOOK**.
   
    ![Créer un utilisateur](./media/spring-cm-tutorial/ic797054.png "Créer un utilisateur")

1. Cliquez sur **Créer l’utilisateur**.

1. Sélectionnez un rôle d’utilisateur dans **User Role**.

1. Sélectionnez **Send Activation Email**.

1. Indiquez le prénom, le nom et l’adresse e-mail du compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.

1. Ajoutez l’utilisateur à un groupe de sécurité dans **Security group**.

1. Cliquez sur **Enregistrer**.

  >[!NOTE]
  >Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par SpringCM, pour approvisionner des comptes utilisateur AAD.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SpringCM.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SpringCM, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **SpringCM**.

    ![Configurer l'authentification unique](./media/spring-cm-tutorial/tutorial_springcm_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Quand vous cliquez sur la vignette SpringCM dans le volet d’accès, vous devez être connecté automatiquement à votre application SpringCM.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/spring-cm-tutorial/tutorial_general_203.png

