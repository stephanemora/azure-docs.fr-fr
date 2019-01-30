---
title: "Tutoriel : Intégration d'Azure Active Directory à Fieldglass | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Fieldglass.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2510195f-d5b1-4684-b3da-283fb8619df2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 0d8c1dc6d0044992692151465aa2b37f6a23445b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822227"
---
# <a name="tutorial-azure-active-directory-integration-with-fieldglass"></a>Tutoriel : Intégration d'Azure Active Directory à Fieldglass

Dans ce didacticiel, vous allez apprendre à intégrer Fieldglass à Azure Active Directory (Azure AD).

L’intégration de Fieldglass à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Fieldglass.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Fieldglass (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Fieldglass, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Fieldglass pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Fieldglass à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-fieldglass-from-the-gallery"></a>Ajout de Fieldglass à partir de la galerie
Pour configurer l’intégration de Fieldglass à Azure AD, vous devez ajouter Fieldglass à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Fieldglass à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **Fieldglass**.

    ![Création d’un utilisateur de test Azure AD](./media/fieldglass-tutorial/tutorial_fieldglass_search.png)

1. Dans le panneau de résultats, sélectionnez **Fieldglass**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/fieldglass-tutorial/tutorial_fieldglass_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Fieldglass à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Fieldglass équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Fieldglass associé doit être établie.

Dans Fieldglass, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Fieldglass, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Fieldglass](#creating-a-fieldglass-test-user)** pour avoir un équivalent de Britta Simon dans Fieldglass lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Fieldglass.

**Pour configurer l’authentification unique Azure AD avec Fieldglass, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Fieldglass**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/fieldglass-tutorial/tutorial_fieldglass_samlbase.png)

1. Dans la section **Fieldglass Domain and URLs** (Domaine et URL Fieldglass), procédez comme suit :

    ![Configurer l'authentification unique](./media/fieldglass-tutorial/tutorial_fieldglass_url.png)

    a. Dans la zone de texte **Identificateur**, entrez une URL comme `https://www.fieldglass.com` ou suivez le modèle : `https://<company name>.fgvms.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant :
    | |
    |--|
    | `https://www.fieldglass.net/<company name>`|
    | `https://<company name>.fgvms.com/<company name>`|

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Fieldglass](https://www.fieldglass.com/customer-support).
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/fieldglass-tutorial/tutorial_fieldglass_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/fieldglass-tutorial/tutorial_general_400.png)

1. Dans la section **Fieldglass Configuration** (Configuration de Fieldglass), cliquez sur **Configure Fieldglass** (Configurer Fieldglass) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configurer l'authentification unique](./media/fieldglass-tutorial/tutorial_fieldglass_configure.png) 

1. Pour configurer l’authentification unique du côté **Fieldglass**, vous devez envoyer le **certificat (Base64)** téléchargé et **l’URL de déconnexion et l’ID d’entité SAML** à [l’équipe de support technique Fieldglass](https://www.fieldglass.com/customer-support). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/fieldglass-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/fieldglass-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/fieldglass-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/fieldglass-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-fieldglass-test-user"></a>Création d’un utilisateur de test Fieldglass

L’objectif de cette section est de créer un utilisateur nommé Britta Simon dans FieldGlass. Travaillez avec [l’équipe de support technique FieldGlass](https://www.fieldglass.com/customer-support) pour ajouter les utilisateurs au compte FieldGlass.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FieldGlass.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Fieldglass, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Fieldglass**.

    ![Configurer l'authentification unique](./media/fieldglass-tutorial/tutorial_fieldglass_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Fieldglass dans le volet d’accès, vous devez être connecté automatiquement à votre application Fieldglass.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fieldglass-tutorial/tutorial_general_01.png
[2]: ./media/fieldglass-tutorial/tutorial_general_02.png
[3]: ./media/fieldglass-tutorial/tutorial_general_03.png
[4]: ./media/fieldglass-tutorial/tutorial_general_04.png

[100]: ./media/fieldglass-tutorial/tutorial_general_100.png

[200]: ./media/fieldglass-tutorial/tutorial_general_200.png
[201]: ./media/fieldglass-tutorial/tutorial_general_201.png
[202]: ./media/fieldglass-tutorial/tutorial_general_202.png
[203]: ./media/fieldglass-tutorial/tutorial_general_203.png

