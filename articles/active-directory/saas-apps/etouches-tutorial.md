---
title: 'Tutoriel : Intégration d’Azure Active Directory à etouches | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 7e4dcc91ecafab6fc0b7864658b0f7724ec04bf2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813914"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutoriel : Intégration d’Azure Active Directory à etouches

Dans ce didacticiel, vous allez apprendre à intégrer etouches à Azure Active Directory (Azure AD).

L’intégration d’etouches à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à etouches.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à etouches (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à etouches, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement etouches pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’etouches à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-etouches-from-the-gallery"></a>Ajout d’etouches à partir de la galerie
Pour configurer l’intégration d’etouches à Azure AD, vous devez ajouter etouches à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter etouches à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **etouches**, sélectionnez **etouches** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![etouches dans la liste des résultats](./media/etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec etouches avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur etouches équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur etouches associé doit être établie.

Dans etouches, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec etouches, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test etouches](#create-an-etouches-test-user)** pour avoir un équivalent de Britta Simon dans etouches lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application etouches.

**Pour configurer l’authentification unique Azure AD avec etouches, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **etouches**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/etouches-tutorial/tutorial_etouches_samlbase.png)

1. Dans la section **Domaine et URL etouches**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL etouches](./media/etouches-tutorial/tutorial_etouches_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous mettez à jour la valeur avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le didacticiel.
    > 

1. L’application etouches attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir des « **Attributs utilisateur** » de l’application. La capture d’écran suivante montre un exemple : 

    ![Attribut utilisateur](./media/etouches-tutorial/tutorial_etouches_attribute.png) 

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |
    | Email | user.mail |    
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Ajouter un attribut](./media/etouches-tutorial/tutorial_attribute_04.png)

    ![Boîte de dialogue Ajouter un attribut](./media/etouches-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**. 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/etouches-tutorial/tutorial_etouches_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/etouches-tutorial/tutorial_general_400.png)

1. Pour configurer l’authentification unique pour votre application, procédez comme suit dans l’application etouches : 

    ![Configuration d’etouches](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Connectez-vous à l’application **etouches** à l’aide des droits d’administrateur.
   
    b. Accédez à la configuration **SAML**.

    c. Dans la section **Paramètres généraux**, ouvrez votre certificat téléchargé à partir du portail Azure dans le Bloc-notes, copiez le contenu, puis collez-le dans la zone de texte des métadonnées IDP. 

    d. Cliquez sur le bouton **Enregistrer et rester**.
  
    e. Dans la section SAML Metadata (Métadonnées SAML), cliquez sur le bouton **Update Metadata (Mettre à jour les métadonnées)** . 

    f. Cela ouvre la page et effectue l’authentification unique. Une fois l’authentification unique effectuée, vous pouvez configurer le nom d’utilisateur.

    g. Dans le champ Nom d’utilisateur, sélectionnez **l’adresse e-mail** comme indiqué sur l’illustration ci-dessous. 

    h. Copiez la valeur de l’**ID d’entité SP** et collez-la dans la zone de texte **Identificateur** de la section **Domaine et URL etouches** du portail Azure.

    i. Copiez la valeur de l’**URL d’authentification unique / ACS** et collez-la dans la zone de texte **URL de connexion** de la section **Domaine et URL etouches** du portail Azure.
   
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/etouches-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/etouches-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Bouton Ajouter](./media/etouches-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/etouches-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-etouches-test-user"></a>Créer un utilisateur de test etouches

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans etouches. Collaborez avec l’[équipe du support technique etouches](https://www.etouches.com/event-software/support/customer-support/) pour ajouter des utilisateurs à la plateforme etouches.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à etouches.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à etouches, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **etouches**.

    ![Lien etouches dans la liste des applications](./media/etouches-tutorial/tutorial_etouches_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique


L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette etouches dans le volet d’accès, vous devez vous connecter automatiquement à votre application etouches.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/etouches-tutorial/tutorial_general_01.png
[2]: ./media/etouches-tutorial/tutorial_general_02.png
[3]: ./media/etouches-tutorial/tutorial_general_03.png
[4]: ./media/etouches-tutorial/tutorial_general_04.png

[100]: ./media/etouches-tutorial/tutorial_general_100.png

[200]: ./media/etouches-tutorial/tutorial_general_200.png
[201]: ./media/etouches-tutorial/tutorial_general_201.png
[202]: ./media/etouches-tutorial/tutorial_general_202.png
[203]: ./media/etouches-tutorial/tutorial_general_203.png

