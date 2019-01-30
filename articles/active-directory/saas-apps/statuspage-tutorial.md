---
title: "Didacticiel : Intégration d'Azure Active Directory à StatusPage | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 0ea399ee510c235eca915416d77cabc1388044f9
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809953"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutoriel : Intégration d'Azure Active Directory à StatusPage

Dans ce didacticiel, vous allez apprendre à intégrer StatusPage à Azure Active Directory (Azure AD).

L’intégration de StatusPage dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à StatusPage.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à StatusPage (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec StatusPage, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement StatusPage pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de StatusPage à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-statuspage-from-the-gallery"></a>Ajout de StatusPage à partir de la galerie
Pour configurer l’intégration de StatusPage avec Azure AD, vous devez ajouter StatusPage à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter StatusPage à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **StatusPage**.

    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/tutorial_statuspage_search.png)

1. Dans le volet de résultats, sélectionnez **StatusPage**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de StatusPage, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur StatusPage équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur StatusPage associé doit être établie.

Dans StatusPage, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec StatusPage, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test StatusPage](#creating-a-statuspage-test-user)** pour avoir un équivalent de Britta Simon dans StatusPage qui soit lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application StatusPage.

**Pour configurer l’authentification unique Azure AD avec StatusPage, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **StatusPage**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

1. Dans la section **Domaine et URL StatusPage**, procédez comme suit :

    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Contactez l’équipe de support StatusPage à l’adresse [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)pour demander les métadonnées nécessaires à la configuration de l’authentification unique. 
    >
    >a. À partir des métadonnées, copiez la valeur de l’émetteur et collez-la dans la zone de texte **Identificateur** .
    >
    >b. À partir des métadonnées, copiez l’URL de réponse et collez-la dans la zone de texte **URL de réponse** .

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de StatusPage** , cliquez sur **Configurer StatusPage** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.

1. Dans la barre d’outils principale, cliquez sur **Manage Account**(Gérer le compte).
   
    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

1. Cliquez sur l’onglet **Single Sign-on** (Authentification unique). 
   
    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

1. Sur la page de configuration de l’authentification unique, procédez comme suit :
   
    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. Dans la zone de texte **URL de cible d’authentification**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    b. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat** . 

    c. Cliquez sur **ENREGISTRER LA CONFIGURATION**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-statuspage-test-user"></a>Création d’un utilisateur de test StatusPage

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans StatusPage.

StatusPage prend en charge l’approvisionnement juste-à-temps. Vous l’avez déjà activé dans [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on).

**Pour créer un utilisateur appelé Britta Simon dans StatusPage, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise StatusPage en tant qu’administrateur.

1. Dans le menu situé en haut, cliquez sur **Manage Account**.

    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Cliquez sur l’onglet **Team Members** (Membres de l’équipe). 
   
    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Cliquez sur l’onglet **ADD TEAM MEMBER** (Ajouter un membre d’équipe). 
   
    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Entrez l’adresse électronique, le prénom et le nom de l’utilisateur valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address** (Adresse électronique), **First Name** (Prénom) et **Surname** (Nom). 
   
    ![Création d’un utilisateur de test Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Pour **Role**, choisissez **Client Administrator**.

1. Cliquez sur **CREATE ACCOUNT** (Créer un compte).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à StatusPage.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à StatusPage, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **StatusPage**.

    ![Configurer l'authentification unique](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette StatusPage dans le volet d’accès, vous devez être connecté automatiquement à votre application StatusPage.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

