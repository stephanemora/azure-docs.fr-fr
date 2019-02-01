---
title: 'Tutoriel : Intégration d’Azure Active Directory à TeamSeer | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 282e95dab879cef064c4a5fd9d478a20eb861bc8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172117"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutoriel : Intégration d’Azure Active Directory à TeamSeer

Dans ce didacticiel, vous allez apprendre à intégrer TeamSeer à Azure Active Directory (Azure AD).

L’intégration de TeamSeer à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TeamSeer.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à TeamSeer (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à TeamSeer, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TeamSeer pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de TeamSeer à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-teamseer-from-the-gallery"></a>Ajout de TeamSeer à partir de la galerie
Pour configurer l’intégration de TeamSeer à Azure AD, vous devez ajouter TeamSeer à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter TeamSeer à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **TeamSeer**.

    ![Création d’un utilisateur de test Azure AD](./media/teamseer-tutorial/tutorial_teamseer_search.png)

1. Dans le panneau de résultats, sélectionnez **TeamSeer**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TeamSeer à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TeamSeer équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur TeamSeer associé doit être établie.

Dans TeamSeer, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec TeamSeer, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test TeamSeer](#creating-a-teamseer-test-user)** pour avoir un équivalent de Britta Simon dans TeamSeer lié à la représentation Azure AD de l’utilisateur.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application TeamSeer.

**Pour configurer l’authentification unique Azure AD avec TeamSeer, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **TeamSeer**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/teamseer-tutorial/tutorial_teamseer_samlbase.png)

1. Dans la section **TeamSeer Domain and URLs** (Domaine et URL TeamSeer), procédez comme suit :

    ![Configurer l'authentification unique](./media/teamseer-tutorial/tutorial_teamseer_url.png)

     Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe de support technique TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html). 
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/teamseer-tutorial/tutorial_teamseer_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/teamseer-tutorial/tutorial_general_400.png)

1. Dans la section **TeamSeer Configuration** (Configuration de TeamSeer), cliquez sur **Configure TeamSeer** (Configurer TeamSeer) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l'authentification unique](./media/teamseer-tutorial/tutorial_teamseer_configure.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise TeamSeer en tant qu’administrateur.

1. Accédez à **HR Admin**.
   
    ![Administrateur RH](./media/teamseer-tutorial/ic789634.png "Administrateur RH")

1. Cliquez sur **Setup**.
   
    ![Configuration](./media/teamseer-tutorial/ic789635.png "Configuration")

1. Cliquez sur **Set up SAML provider details**.
   
    ![Paramètres SAML](./media/teamseer-tutorial/ic789636.png "Paramètres SAML")

1. Dans la section des détails sur le fournisseur SAML, procédez comme suit :
   
    ![Paramètres SAML](./media/teamseer-tutorial/ic789637.png "Paramètres SAML")   

    a. Collez la valeur de **l’URL du service d’authentification unique** dans la zone de texte **URL**.
          
    b. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IdP Public Certificate** (Certificat public de fournisseur d’identité).

1. Pour configurer le fournisseur SAML, procédez comme suit :
    
    ![Paramètres SAML](./media/teamseer-tutorial/ic789638.png "Paramètres SAML") 

    a. Dans la zone de test **Tester l’adresse de messagerie**, entrez l’adresse de messagerie de l’utilisateur de test. 
  
    b. Dans la zone de texte **Émetteur** , entrez l’URL de l’émetteur du fournisseur du service. 
  
    c. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/teamseer-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/teamseer-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/teamseer-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/teamseer-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-teamseer-test-user"></a>Création d’un utilisateur de test TeamSeer

Pour se connecter à TeamSeer, les utilisateurs d’Azure AD doivent être approvisionnés dans TeamSeer. Dans le cas de TeamSeer, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise **TeamSeer** en tant qu’administrateur.

1. Procédez comme suit :
   
    ![Administrateur RH](./media/teamseer-tutorial/ic789640.png "Administrateur RH")  
 
    a. Accédez à **HR Admin \> Users**.
  
    b. Cliquez sur **Run the New User wizard**.

1. Dans la section **User Details** , procédez comme suit :
   
    ![Détails de l’utilisateur](./media/teamseer-tutorial/ic789641.png "Détails de l’utilisateur")

    a. Dans les zones de texte correspondantes, indiquez le **prénom**, le **nom** et le **nom d’utilisateur (adresse de messagerie)** d’un compte AAD valide que vous souhaitez approvisionner.
  
    b. Cliquez sur **Suivant**.

1. Suivez les instructions à l’écran pour ajouter un nouvel utilisateur, puis cliquez sur **Terminer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par TeamSeer, pour approvisionner des comptes utilisateur AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TeamSeer.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à TeamSeer, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **TeamSeer**.

    ![Configurer l'authentification unique](./media/teamseer-tutorial/tutorial_teamseer_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/teamseer-tutorial/tutorial_general_01.png
[2]: ./media/teamseer-tutorial/tutorial_general_02.png
[3]: ./media/teamseer-tutorial/tutorial_general_03.png
[4]: ./media/teamseer-tutorial/tutorial_general_04.png

[100]: ./media/teamseer-tutorial/tutorial_general_100.png

[200]: ./media/teamseer-tutorial/tutorial_general_200.png
[201]: ./media/teamseer-tutorial/tutorial_general_201.png
[202]: ./media/teamseer-tutorial/tutorial_general_202.png
[203]: ./media/teamseer-tutorial/tutorial_general_203.png

