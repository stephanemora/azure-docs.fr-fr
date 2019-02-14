---
title: 'Tutoriel : Intégration d’Azure Active Directory à Shmoop For Schools | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Shmoop For Schools.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 095aff293b23f4589dce9f71890b5e9bfd8636d4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201854"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Tutoriel : Intégration d’Azure Active Directory à Shmoop For Schools

Dans ce didacticiel, vous allez apprendre à intégrer Shmoop For Schools à Azure Active Directory (Azure AD).

L’intégration de Shmoop For Schools à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Shmoop For Schools.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Shmoop For Schools avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Shmoop For Schools, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Shmoop For Schools avec authentification unique

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester les étapes de ce didacticiel, voici quelques recommandons :

- N’utilisez votre environnement de production que si nécessaire.
- Obtenez un [essai d’un mois](https://azure.microsoft.com/pricing/free-trial/) si vous n’avez pas déjà un environnement d’essai Azure AD.

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Shmoop For Schools à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Ajouter Shmoop For Schools à partir de la galerie
Pour configurer l’intégration de Shmoop For Schools à Azure AD, vous devez ajouter Shmoop For Schools dans votre liste d’applications SaaS managées à partir de la galerie.

**Pour ajouter Shmoop For Schools à partir de la galerie, procédez comme suit :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Shmoop For Schools**. Sélectionnez **Shmoop For Schools** dans les résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l'application.

    ![Shmoop For Schools dans la liste des résultats](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Shmoop For Schools avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Shmoop For Schools équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Shmoop For Schools.

Pour configurer et tester l’authentification unique Azure AD avec Shmoop For Schools, suivez les indications des sections ci-après :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Shmoop For Schools](#create-a-shmoop-for-schools-test-user) pour avoir un équivalent de Britta Simon dans Shmoop For Schools qui soit associé à la représentation de l’utilisateur Azure AD.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Shmoop For Schools.

**Pour configurer l’authentification unique Azure AD avec Shmoop For Schools, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Shmoop For Schools**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, dans le menu déroulant **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. Dans la section **Domaine et URL Shmoop For Schools**, procédez comme suit :

    ![Configurer l’authentification unique](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support du client Shmoop For Schools](mailto:support@shmoop.com). 
 
4. L’application Shmoop For Schools attend les assertions SAML dans un format précis. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre comment configurer les assertions :

    ![Configurer l’authentification unique](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools prend en charge deux rôles pour les utilisateurs : **Teacher** et **Student**. Configurez ces rôles dans Azure AD pour pouvoir affecter les rôles appropriés aux utilisateurs. Pour comprendre comment configurer des rôles dans Azure AD, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l'attribut de jeton SAML comme illustré dans l'image précédente.  Ensuite, effectuez les étapes suivantes :

    | Nom de l’attribut | Valeur de l’attribut |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. Pour ouvrir la boîte de dialogue **Ajouter un attribut**, cliquez sur **Ajouter un attribut**.
    
    ![Configurer l’authentification unique ](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Configurer l’authentification unique](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur**, sélectionnez la valeur de l’attribut pour cette ligne.

    d. Laissez la zone de texte **Espace de noms** vide.
    
    e. Sélectionnez **OK**.

6. Sélectionnez le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. Dans la section  **Certificat de signature SAML** , cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Pour configurer l’authentification unique côté **Shmoop For Schools**, vous devez envoyer **l’URL des métadonnées de fédération de l’application** à [l’équipe de support de Shmoop For Schools](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**. Puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Créer un utilisateur de test Shmoop For Schools

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Shmoop For Schools. Shmoop For Schools prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n'existe pas déjà, il est créé lors de la tentative d’accès à Shmoop For Schools.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support de Shmoop For Schools](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Shmoop For Schools.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Shmoop For Schools, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications. Accédez ensuite à **Applications d’entreprise** dans la vue d'annuaire.  Ensuite, sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Shmoop For Schools**.

    ![Lien Shmoop For Schools dans la liste des applications](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste d’utilisateurs.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**. 

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous sélectionnez la vignette **Shmoop For Schools** dans le volet d’accès, vous devez être connecté automatiquement à votre application Shmoop For Schools.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

