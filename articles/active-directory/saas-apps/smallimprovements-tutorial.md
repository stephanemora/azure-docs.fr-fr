---
title: 'Tutoriel : Intégration d’Azure Active Directory à Small Improvements | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Small Improvements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 27f94ef7fb41315ea11f87adf8a4f7f026d584af
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818657"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Tutoriel : Intégration d’Azure Active Directory à Small Improvements

Dans ce didacticiel, vous allez apprendre à intégrer Small Improvements à Azure Active Directory (Azure AD).

L’intégration de Small Improvements à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Small Improvements.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Small Improvements (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Small Improvements, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Small Improvements pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici [Offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Small Improvements à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-small-improvements-from-the-gallery"></a>Ajout de Small Improvements à partir de la galerie
Pour configurer l’intégration de Small Improvements avec Azure AD, vous devez ajouter Small Improvements à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Small Improvements à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Small Improvements**.

    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

1. Dans le volet de résultats, sélectionnez **Small Improvements**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD après de Small Improvements, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Small Improvements équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Small Improvements associé doit être établie.

Dans Small Improvements, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Small Improvements, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Small Improvements](#creating-a-small-improvements-test-user)** pour avoir un équivalent de Britta Simon dans Small Improvements lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Small Improvements.

**Pour configurer l’authentification unique Azure AD avec Small Improvements, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Small Improvements**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

1. Dans la section **Domaine et URL Small Improvements**, procédez comme suit :

    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.small-improvements.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez l’[équipe de support client Small Improvements](mailto:support@small-improvements.com) pour obtenir ces valeurs. 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Small Improvements**, cliquez sur **Configurer Small Improvements** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

1. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise Small Improvements en tant qu’administrateur.

1. Sur la page du tableau de bord principal, cliquez sur le bouton **Administration** situé sur la gauche.
   
    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Cliquez sur le bouton **SAML SSO** de la section **Integrations**.
   
    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Sur la page de configuration de l’authentification unique, procédez comme suit :
   
    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Dans la zone de texte **Point de terminaison HTTP**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    b. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat x509** . 

    c. Si vous souhaitez que les utilisateurs disposent de l’authentification unique et de l’option d’authentification par formulaire de connexion, activez l’option **Activer l’accès également via une connexion/un mot de passe**.  

    d. Entrez les informations appropriées pour nommer le bouton de connexion d’authentification unique dans la zone de texte **SAML Prompt** .  

    e. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-small-improvements-test-user"></a>Création d'un utilisateur de test Small Improvements

Pour se connecter à Small Improvements, les utilisateurs d’Azure AD doivent être approvisionnés dans Small Improvements. Dans le cas de Small Improvements, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Small Improvements en tant qu’administrateur.

1. À partir de la page d’accueil, accédez au menu de gauche, puis cliquez sur **Administration**.

1. Cliquez sur le bouton **User Directory** dans la section User Management. 
   
    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Cliquez sur **Add Users**.

    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Dans la boîte de dialogue **Add users** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Entrez le **prénom** de l’utilisateur, à savoir **Britta**.

    b. Entrez le **nom de famille** de l’utilisateur, à savoir **Simon**.

    c. Entrez l’**adresse e-mail** de l’utilisateur, à savoir <strong>brittasimon@contoso.com</strong>. 

    d. Vous pouvez également choisir d’entrer le message personnel dans la zone **Envoyer un e-mail de notification** . Décochez cette case si vous ne souhaitez envoyer de notification.

    e. Cliquez sur **Créer des utilisateurs**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Small Improvements.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Small Improvements, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Small Improvements**.

    ![Configurer l'authentification unique](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Lorsque vous cliquez sur la vignette Small Improvements dans le volet d’accès, vous devez être connecté automatiquement à votre application Small Improvements.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

