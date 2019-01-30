---
title: "Tutoriel : Intégration d'Azure Active Directory à Kindling | Microsoft Docs"
description: Découvrez comment configurer l'authentification unique entre Azure Active Directory et Kindling.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 71229751-74eb-4c2c-abb4-07caa95754c7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 0ee91afecb9c98249c4545bee244e8f4d2e2fc21
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822363"
---
# <a name="tutorial-azure-active-directory-integration-with-kindling"></a>Tutoriel : Intégration d'Azure Active Directory à Kindling

L’objectif de ce didacticiel est de vous apprendre à intégrer Kindling à Azure Active Directory (Azure AD).

L’intégration de Kindling à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Kindling
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Kindling (via l'authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration de l’application SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l'intégration d'Azure AD avec Kindling, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Kindling pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kindling à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-kindling-from-the-gallery"></a>Ajout de Kindling à partir de la galerie
Pour configurer l'intégration de Kindling à Azure AD, vous devez ajouter Kindling à votre liste d'applications SaaS gérées à partir de la galerie.

**Pour ajouter Kindling à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Kindling**.

    ![Création d’un utilisateur de test Azure AD](./media/kindling-tutorial/tutorial_kindling_search.png)

1. Dans le panneau de résultats, sélectionnez **Kindling**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/kindling-tutorial/tutorial_kindling_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Kindling, grâce à un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Kindling équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Kindling associé.

Pour cela, affectez la valeur du **nom d'utilisateur** d'Azure AD comme valeur du **nom d'utilisateur** dans Kindling.

Pour configurer et tester l'authentification unique Azure AD avec Kindling, vous devez suivre les blocs élémentaires suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d'un utilisateur de test Kindling](#creating-a-kindling-test-user)** pour avoir un équivalent de Britta Simon dans Kindling lié à la représentation d’un utilisateur Azure AD.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Kindling.

**Pour configurer l'authentification unique Azure AD avec Kindling, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Kindling**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/kindling-tutorial/tutorial_kindling_samlbase.png)

1. Dans la section **Domaine et URL Kindling**, procédez comme suit :

    ![Configurer l'authentification unique](./media/kindling-tutorial/tutorial_kindling_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.kindlingapp.com`

    b.  Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.kindlingapp.com/saml/module.php/saml/sp/metadata.php/clientIDP`

    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Kindling](mailto:support@kindlingapp.com).
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/kindling-tutorial/tutorial_kindling_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/kindling-tutorial/tutorial_general_400.png)

1. Pour ouvrir la fenêtre **Configurer l’authentification**, dans la section **Configuration de Kindling**, cliquez sur **Configurer Kindling**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/kindling-tutorial/tutorial_kindling_configure.png) 

1. Pour configurer l’authentification unique côté **Kindling**, vous devez envoyer le **Certificat (Base64)** téléchargé, l’**URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à l’[équipe de support Kindling](mailto:support@kindlingapp.com).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/kindling-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/kindling-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/kindling-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/kindling-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-kindling-test-user"></a>Création d'un utilisateur de test Kindling

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Kindling. Kindling prend en charge l'approvisionnement juste-à-temps. Vous l'avez déjà activé dans [Configuration de l'authentification unique Azure AD](#configuring-azure-ad-single-sign-on).

Vous n’avez aucune opération à effectuer dans cette section.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kindling.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Kindling, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Kindling**.

    ![Configurer l'authentification unique](./media/kindling-tutorial/tutorial_kindling_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Kindling dans le volet d'accès, vous êtes connecté automatiquement à votre application Kindling. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kindling-tutorial/tutorial_general_01.png
[2]: ./media/kindling-tutorial/tutorial_general_02.png
[3]: ./media/kindling-tutorial/tutorial_general_03.png
[4]: ./media/kindling-tutorial/tutorial_general_04.png

[100]: ./media/kindling-tutorial/tutorial_general_100.png

[200]: ./media/kindling-tutorial/tutorial_general_200.png
[201]: ./media/kindling-tutorial/tutorial_general_201.png
[202]: ./media/kindling-tutorial/tutorial_general_202.png
[203]: ./media/kindling-tutorial/tutorial_general_203.png

