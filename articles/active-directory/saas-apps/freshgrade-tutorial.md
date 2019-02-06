---
title: 'Tutoriel : Intégration d’Azure Active Directory à FreshGrade | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FreshGrade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1055bba6-f4df-462e-bc9b-1ad5ada0f638
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 1861dfb23d105fa6b403095b5fc36d1f2a894fe9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152552"
---
# <a name="tutorial-azure-active-directory-integration-with-freshgrade"></a>Tutoriel : Intégration d’Azure Active Directory à FreshGrade

Dans ce didacticiel, vous allez apprendre à intégrer FreshGrade à Azure Active Directory (Azure AD).

L’intégration de FreshGrade dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à FreshGrade.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FreshGrade (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans FreshGrade, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement FreshGrade pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FreshGrade depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-freshgrade-from-the-gallery"></a>Ajout de FreshGrade depuis la galerie
Pour configurer l’intégration de FreshGrade à Azure AD, vous devez ajouter FreshGrade depuis la galerie à votre liste d’applications SaaS gérées.

**Procédez comme suit pour ajouter FreshGrade à partir de la galerie :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **FreshGrade**.

    ![Création d’un utilisateur de test Azure AD](./media/freshgrade-tutorial/tutorial_freshgrade_search.png)

1. Dans le volet de résultats, sélectionnez **FreshGrade**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/freshgrade-tutorial/tutorial_freshgrade_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et testez l’authentification unique Azure AD avec FreshGrade avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur test FreshGrade équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur FreshGrade associé doit être établie.

Dans FreshGrade, assignez la valeur du **nom d’utilisateur** indiquée dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec FreshGrade, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test FreshGrade](#creating-a-freshgrade-test-user)** pour avoir un équivalent de Britta Simon dans FreshGrade lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application FreshGrade.

**Procédez comme suit pour configurer l’authentification unique Azure AD avec FreshGrade :**

1. Sur le portail Azure, à la page d’intégration de l’application **FreshGrade**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Configurer l'authentification unique](./media/freshgrade-tutorial/tutorial_freshgrade_samlbase.png)

1. Dans la section **Domaine et URL FreshGrade**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/freshgrade-tutorial/tutorial_freshgrade_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :
      | |
      |--|
      | `https://<subdomain>.freshgrade.com/login` |
      | `https://<subdomain>.onboarding.freshgrade.com/login` |

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :
      | |
      |--|
      | `https://login.onboarding.freshgrade.com:443/saml/metadata/alias/<instancename>` |
      | `https://login.freshgrade.com:443/saml/metadata/alias/<instancename>` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de prise en charge des clients FreshGrade](mailTo:support@freshgrade.com).

1. Dans la section  **Certificat de signature SAML** , cliquez sur le bouton Copier pour copier l’ **URL des métadonnées de fédération de l’application** , puis collez-la dans le Bloc-notes.
    
    ![Configurer l'authentification unique](./media/freshgrade-tutorial/tutorial_metadataurl.png)
     
1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/freshgrade-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de FreshGrade**, cliquez sur **Configurer FreshGrade** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/freshgrade-tutorial/tutorial_freshgrade_configure.png)

1. Pour configurer l’authentification unique côté **FreshGrade**, vous devez envoyer **l’URL des métadonnées de fédération de l’application** et **l’URL du service d’authentification unique SAML** à [l’équipe du support technique FreshGrade](mailTo:support@freshgrade.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/freshgrade-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/freshgrade-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/freshgrade-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/freshgrade-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-freshgrade-test-user"></a>Création d’un utilisateur de test FreshGrade

Dans cette section, vous créez un utilisateur appelé Britta Simon dans FreshGrade. Collaborez avec l’[équipe de support technique FreshGrade](mailTo:support@freshgrade.com) pour ajouter les utilisateurs dans la plateforme FreshGrade.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FreshGrade.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à FreshGrade, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **FreshGrade**.

    ![Configurer l'authentification unique](./media/freshgrade-tutorial/tutorial_freshgrade_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette FreshGrade dans le volet d’accès, vous devez être connecté automatiquement à votre application FreshGrade.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshgrade-tutorial/tutorial_general_01.png
[2]: ./media/freshgrade-tutorial/tutorial_general_02.png
[3]: ./media/freshgrade-tutorial/tutorial_general_03.png
[4]: ./media/freshgrade-tutorial/tutorial_general_04.png

[100]: ./media/freshgrade-tutorial/tutorial_general_100.png

[200]: ./media/freshgrade-tutorial/tutorial_general_200.png
[201]: ./media/freshgrade-tutorial/tutorial_general_201.png
[202]: ./media/freshgrade-tutorial/tutorial_general_202.png
[203]: ./media/freshgrade-tutorial/tutorial_general_203.png

