---
title: "Tutoriel : Intégration d'Azure Active Directory à iLMS | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68f76f6ba451ff5f3e81b3aaabf3825155ba15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201531"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Tutoriel : Intégration d'Azure Active Directory à iLMS

Dans ce didacticiel, vous allez apprendre à intégrer iLMS dans Azure Active Directory (Azure AD).

L’intégration d’iLMS dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à iLMS
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à iLMS (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec iLMS, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement iLMS pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’iLMS à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ilms-from-the-gallery"></a>Ajout d’iLMS à partir de la galerie
Pour configurer l’intégration d’iLMS avec Azure AD, vous devez ajouter iLMS à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter iLMS à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter la nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, tapez **iLMS**.

    ![Création d’un utilisateur de test Azure AD](./media/ilms-tutorial/tutorial_ilms_search.png)

1. Dans le volet de résultats, sélectionnez **iLMS**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec iLMS avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur iLMS équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur iLMS associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans iLMS.

Pour configurer et tester l’authentification unique Azure AD avec iLMS, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test iLMS](#creating-an-ilms-test-user)** pour avoir un équivalent de Britta Simon dans iLMS lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application iLMS.

**Pour configurer l’authentification unique Azure AD avec iLMS, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **iLMS**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_ilms_samlbase.png)

1. Dans la section **Domaines et URL iLMS**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP**:

    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_ilms_url.png)

    a. Dans la zone de texte **Identificateur**, collez la valeur **Identificateur** que vous copiez à partir de la section **Fournisseur de services** des paramètres SAML dans le portail d’administration iLMS.

    b. Dans la zone de texte **URL de réponse**, collez la valeur **Point de terminaison (URL)** que vous copiez à partir de la section **Fournisseur de services** des paramètres SAML dans le portail d’administration iLMS, avec le modèle suivant `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >« 123456 » est un exemple de valeur d’identificateur.

1. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service** :

    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_ilms_url1.png)

    Dans la zone de texte **URL de connexion**, collez la valeur **Point de terminaison (URL)** que vous copiez à partir de la section **Fournisseur de services** des paramètres SAML dans le portail d’administration en tant que `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

1. Pour activer l’approvisionnement JIT, l’application iLMS attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configurer l'authentification unique](./media/ilms-tutorial/4.png)
    
    Créez les attributs **Département, région** et **Division** et ajoutez le nom de ces attributs dans iLMS. Tous les attributs présentés ci-dessus sont requis.  

    > [!NOTE] 
    > Vous devez activer **Créer un compte utilisateur non reconnu** dans iLMS pour mapper ces attributs. Suivez les instructions [ici](http://support.inspiredelearning.com/customer/portal/articles/2204526) pour avoir une idée sur la configuration des attributs.

1. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_04.png)

    ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_general_400.png)

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration iLMS** en tant qu’administrateur.

1. Cliquez sur **SSO:SAML** sous l’onglet **Paramètres** pour ouvrir les paramètres SAML et effectuer les opérations suivantes :
    
    ![Configurer l'authentification unique](./media/ilms-tutorial/1.png) 

    a. Développez la section **Fournisseur de services** et copiez les valeurs **Identificateur** et **URL du point de terminaison**.

    ![Configurer l'authentification unique](./media/ilms-tutorial/2.png) 

    b. Sous la section **Fournisseur d’identité**, cliquez sur **importer les métadonnées**.
    
    c. Sélectionnez le fichier **Métadonnées** téléchargé à partir du portail Azure à partir de la section **Certificat de signature SAML**.

    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Si vous souhaitez activer JIT pour approvisionner des comptes iLMS pour l’annulation de reconnaissance d’utilisateurs, procédez comme suit :
        
       - Cochez **Créer un compte utilisateur non reconnu**.
       
       ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mappez les attributs dans Azure AD avec les attributs dans iLMS. Dans la colonne d’attribut, spécifiez le nom des attributs ou la valeur par défaut.

    e. Accédez à l’onglet **Règles d’entreprise** et effectuez les opérations suivantes : 
        
       ![Configure Single Sign-On](./media/ilms-tutorial/5.png)

       - Cochez **Créer des régions, divisions et départements non reconnus** pour créer des régions, divisions et départements qui n’existent pas encore au moment de l’authentification unique.
        
       - Cochez **Mettre à jour le profil utilisateur lors de la connexion** pour spécifier si le profil utilisateur est mis à jour à chaque ouverture de session unique. 
        
       - Si l’option **Mettre à jour les valeurs vides pour les champs non obligatoires dans le profil utilisateur** est cochée, les champs facultatifs qui sont vides lors de la connexion provoqueront également la présence de valeurs vides pour ces champs dans le profil iLMS de l’utilisateur.
        
       - Cochez **Envoyer un e-mail de notification d’erreur** et entrez l’adresse e-mail de l’utilisateur pour lequel vous souhaitez recevoir l’e-mail de notification d’erreur.

1. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

    ![Configurer l'authentification unique](./media/ilms-tutorial/save.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/ilms-tutorial/create_aaduser_01.png) 

1. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/ilms-tutorial/create_aaduser_02.png) 

1. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/ilms-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/ilms-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-an-ilms-test-user"></a>Création d’un utilisateur de test iLMS

L’application prend en charge la configuration d’utilisateur juste-à-temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. JIT fonctionnera si vous avez coché la case **Créer un compte utilisateur non reconnu** lors de la configuration de SAML sur le portail d’administration iLMS.

Si vous avez besoin de créer un utilisateur manuellement, suivez les étapes ci-dessous :

1. Connectez-vous à votre site d’entreprise iLMS en tant qu’administrateur.

1. Cliquez sur **Inscrire un utilisateur** sous l’onglet **Utilisateurs** pour ouvrir la page **Inscrire un utilisateur**. 
   
   ![Ajouter un employé](./media/ilms-tutorial/3.png)

1. Dans la page **Inscrire un utilisateur**, procédez comme suit.

    ![Ajouter un employé](./media/ilms-tutorial/create_testuser_add.png)

    a. Dans la zone de texte **First Name** (Prénom), tapez le prénom, par exemple Britta.
   
    b. Dans la zone de texte **Last Name** (Nom), tapez le nom, par exemple Simon.

    c. Dans la zone de texte **ID d’e-mail** , tapez l’adresse de messagerie du compte de Britta Simon.

    d. Dans la liste déroulante **Région**, sélectionnez la valeur pour la région.

    e. Dans la liste déroulante **Division**, sélectionnez la valeur pour la division.

    f. Dans la liste déroulante **Département**, sélectionnez une valeur pour le département.

    g. Cliquez sur **Enregistrer**.

    > [!NOTE] 
    > Vous pouvez envoyer un courrier d’inscription à l’utilisateur en cochant la case **Send Registration Mail** (Envoyer un e-mail d’inscription).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iLMS.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à iLMS, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **iLMS**.

    ![Configurer l'authentification unique](./media/ilms-tutorial/tutorial_ilms_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque iLMS dans le volet d’accès, vous devez être connecté automatiquement à votre application iLMS.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ilms-tutorial/tutorial_general_01.png
[2]: ./media/ilms-tutorial/tutorial_general_02.png
[3]: ./media/ilms-tutorial/tutorial_general_03.png
[4]: ./media/ilms-tutorial/tutorial_general_04.png

[100]: ./media/ilms-tutorial/tutorial_general_100.png

[200]: ./media/ilms-tutorial/tutorial_general_200.png
[201]: ./media/ilms-tutorial/tutorial_general_201.png
[202]: ./media/ilms-tutorial/tutorial_general_202.png
[203]: ./media/ilms-tutorial/tutorial_general_203.png

