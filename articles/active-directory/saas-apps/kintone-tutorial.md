---
title: 'Didacticiel : intégration d’Azure Active Directory avec Kintone | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 693211245ee98849548bee4a52f7e424dd8b4cc6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430455"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Didacticiel : intégration d’Azure Active Directory avec Kintone

L’objectif de ce didacticiel est de vous apprendre à intégrer Kintone avec Azure Active Directory (Azure AD).

Intégrer Kintone avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler l’accès à Kintone
- Vous pouvez autoriser la connexion automatique à Kintone (via l’authentification unique) pour vos utilisateurs avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Kintone, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Kintone pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kintone à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-kintone-from-the-gallery"></a>Ajout de Kintone à partir de la galerie
Pour configurer l’intégration de Kintone avec Azure AD, vous devez ajouter Kintone, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Kintone à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Kintone**.

    ![Création d’un utilisateur de test Azure AD](./media/kintone-tutorial/tutorial_kintone_search.png)

1. Dans le panneau de résultats, sélectionnez **Kintone**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Kintone, grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Kintone correspondant dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Kintone associé.

Dans Kintone, assigner la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Kintone, vous devez compléter les blocs de construction suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test Kintone](#creating-a-kintone-test-user)** pour avoir un équivalent de Britta Simon dans Kintone lié à la représentation d’un utilisateur Azure AD.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Kintone.

**Pour configurer l’authentification unique Azure AD avec Kintone, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Kintone**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/kintone-tutorial/tutorial_kintone_samlbase.png)

1. Dans la section **Domaine et URL Kintone**, procédez comme suit :

    ![Configurer l'authentification unique](./media/kintone-tutorial/tutorial_kintone_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.kintone.com`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Kintone](https://www.kintone.com/contact/). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/kintone-tutorial/tutorial_kintone_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/kintone-tutorial/tutorial_general_400.png)

1. Pour ouvrir la fenêtre **Configurer l’authentification**, dans la section **Configuration Kintone**, cliquez sur **Configurer Kintone**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l'authentification unique](./media/kintone-tutorial/tutorial_kintone_configure.png) 

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **Kintone** en tant qu’administrateur.

1. Cliquez sur **Settings**.
   
    ![Paramètres](./media/kintone-tutorial/ic785879.png "Paramètres")

1. Cliquez sur **Users & System Administration**.
   
    ![Administration système et utilisateurs](./media/kintone-tutorial/ic785880.png "Administration système et utilisateurs")

1. Sous **System Administration \> Security**, cliquez sur **Login**.
   
    ![Connexion](./media/kintone-tutorial/ic785881.png "Connexion")

1. Cliquez sur **Enable SAML authentication**.
   
    ![Authentification SAML](./media/kintone-tutorial/ic785882.png "Authentification SAML")

1. Dans la section SAML Authentication, procédez comme suit :
    
    ![Authentification SAML](./media/kintone-tutorial/ic785883.png "Authentification SAML")
    
    a. Dans la zone de texte **URL de connexion**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    b. Dans la zone de texte **URL de déconnexion**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
    
    c. Cliquez sur **Parcourir** pour charger le certificat téléchargé.
    
    d. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/kintone-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/kintone-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/kintone-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/kintone-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-kintone-test-user"></a>Création d’un utilisateur de test Kintone

Pour que les utilisateurs Azure AD puissent se connecter à Kintone, ils doivent être approvisionnés dans Kintone.  
Dans le cas de Kintone, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise **Kintone** en tant qu’administrateur.

1. Cliquez sur **Setting**.
   
    ![Paramètres](./media/kintone-tutorial/ic785879.png "Paramètres")

1. Cliquez sur **Users & System Administration**.
   
    ![Administration système et utilisateur](./media/kintone-tutorial/ic785880.png "Administration système et utilisateur")

1. Sous **User Administration**, cliquez sur **Departments & Users**.
   
    ![Département et utilisateurs](./media/kintone-tutorial/ic785888.png "Département et utilisateurs")

1. Cliquez sur **New User**.
   
    ![Nouveaux utilisateurs](./media/kintone-tutorial/ic785889.png "Nouveaux utilisateurs")

1. Dans la section **New User**, procédez comme suit :
   
    ![Nouveaux utilisateurs](./media/kintone-tutorial/ic785890.png "Nouveaux utilisateurs")
   
    a. Dans les zones de texte correspondantes, entrez un **Nom d’affichage**, un **Nom de connexion**, un **Nouveau mot de passe**, confirmez-le dans **Confirmer le mot de passe**, entrez ainsi une **adresse e-mail** et d’autres détails d’un compte AAD valide à provisionner.
 
    b. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Kintone fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant un accès à Kintone.

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à Kintone, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Kintone**.

    ![Configurer l'authentification unique](./media/kintone-tutorial/tutorial_kintone_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

En cliquant sur la vignette Kintone dans le Panneau d’accès, vous allez en principe être connecté automatiquement à votre application Kintone.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kintone-tutorial/tutorial_general_01.png
[2]: ./media/kintone-tutorial/tutorial_general_02.png
[3]: ./media/kintone-tutorial/tutorial_general_03.png
[4]: ./media/kintone-tutorial/tutorial_general_04.png

[100]: ./media/kintone-tutorial/tutorial_general_100.png

[200]: ./media/kintone-tutorial/tutorial_general_200.png
[201]: ./media/kintone-tutorial/tutorial_general_201.png
[202]: ./media/kintone-tutorial/tutorial_general_202.png
[203]: ./media/kintone-tutorial/tutorial_general_203.png

