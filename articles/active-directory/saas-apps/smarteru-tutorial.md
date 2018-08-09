---
title: 'Didacticiel : Intégration d’Azure Active Directory à SmarterU | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 12c7a2751b980ef7951be9043a62dbb1ec50a63d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439759"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Didacticiel : Intégration d’Azure Active Directory à SmarterU

Dans ce didacticiel, vous allez apprendre à intégrer SmarterU à Azure Active Directory (Azure AD).

L’intégration de SmarterU dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SmarterU.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SmarterU (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SmarterU, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SmarterU pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SmarterU à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-smarteru-from-the-gallery"></a>Ajout de SmarterU à partir de la galerie
Pour configurer l’intégration de SmarterU à Azure AD, vous devez ajouter SmarterU à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SmarterU à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **SmarterU**.

    ![Création d’un utilisateur de test Azure AD](./media/smarteru-tutorial/tutorial_smarteru_search.png)

1. Dans le volet de résultats, sélectionnez **SmarterU**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/smarteru-tutorial/tutorial_smarteru_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SmarterU, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SmarterU correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SmarterU associé doit être établie.

Dans SmarterU, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SmarterU, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test SmarterU](#creating-a-smarteru-test-user)** pour avoir un équivalent de Britta Simon dans SmarterU lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SmarterU.

**Pour configurer l’authentification unique Azure AD avec SmarterU, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **SmarterU**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/smarteru-tutorial/tutorial_smarteru_samlbase.png)

1. Dans la section **Domaine et URL SmarterU**, procédez comme suit : 

    ![Configurer l'authentification unique](./media/smarteru-tutorial/tutorial_smarteru_url.png)

    Dans la zone de texte **Identificateur**, saisissez l’URL : `https://www.smarteru.com/`

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/smarteru-tutorial/tutorial_smarteru_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/smarteru-tutorial/tutorial_general_400.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise SmarterU en tant qu’administrateur.

1. Dans la barre d’outils située en haut, cliquez sur **Account Settings**.
   
    ![Account Settings](./media/smarteru-tutorial/accountsettings.png)

1. Dans la page de configuration du compte, procédez comme suit :
   
    ![External Authorization](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 
 
      a. Sélectionnez **Enable External Authorization**.
  
      b. Dans la section **Master Login Control**, cliquez sur l’onglet **SmarterU**.
  
      c. Dans la section **User Default Login**, cliquez sur l’onglet **SmarterU**.
  
      d. Sélectionnez **Enable SAML**.
  
      e. Copiez le contenu du fichier de métadonnées téléchargé et collez-le dans la zone de texte **IdP Metadata** (Métadonnées IdP).
      
      f. Sélectionnez un élément **Identifier Attribute/Claim** (Attribut d’identificateur/Revendication).
  
      g. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/smarteru-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/smarteru-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/smarteru-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/smarteru-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-smarteru-test-user"></a>Création d’un utilisateur de test SmarterU

Pour se connecter à SmarterU, les utilisateurs d’Azure AD doivent être approvisionnés dans SmarterU.

Pour SmarterU, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **SmarterU** .

1. Accédez à **Users**.

1. Dans la section Users, procédez comme suit :
   
    ![Nouvel utilisateur](./media/smarteru-tutorial/adduser.png)  

    a. Cliquez sur **+User**.
    
    b. Saisissez les valeurs d’attribut associées au compte d’utilisateur Azure AD dans les zones de texte **Primary Email**, **Employee ID**, **Password**, **Verify Password**, **Given Name** et **Surname**.
    
    c. Cliquez sur **Active**. 
    
    d. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par SmarterU, pour approvisionner des comptes utilisateur AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SmarterU.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SmarterU, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **SmarterU**.

    ![Configurer l'authentification unique](./media/smarteru-tutorial/tutorial_smarteru_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Quand vous cliquez sur la vignette SmarterU dans le volet d’accès, vous devez être connecté automatiquement à votre application SmarterU.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smarteru-tutorial/tutorial_general_01.png
[2]: ./media/smarteru-tutorial/tutorial_general_02.png
[3]: ./media/smarteru-tutorial/tutorial_general_03.png
[4]: ./media/smarteru-tutorial/tutorial_general_04.png

[100]: ./media/smarteru-tutorial/tutorial_general_100.png

[200]: ./media/smarteru-tutorial/tutorial_general_200.png
[201]: ./media/smarteru-tutorial/tutorial_general_201.png
[202]: ./media/smarteru-tutorial/tutorial_general_202.png
[203]: ./media/smarteru-tutorial/tutorial_general_203.png

