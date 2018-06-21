---
title: 'Tutoriel : Intégration d’Azure Active Directory à AwardSpring | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AwardSpring.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f115be6-4fbe-42aa-9319-7462e7a75736
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: e9c67a9ac733a1dd7e4810f3f8a3185ff7142041
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212602"
---
# <a name="tutorial-azure-active-directory-integration-with-awardspring"></a>Tutoriel : Intégration d’Azure Active Directory à AwardSpring

Dans ce tutoriel, vous allez découvrir comment intégrer AwardSpring à Azure Active Directory (Azure AD).

L’intégration d’AwardSpring dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à AwardSpring
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à AwardSpring (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec AwardSpring, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement AwardSpring pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’AwardSpring à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-awardspring-from-the-gallery"></a>Ajout d’AwardSpring à partir de la galerie
Pour configurer l’intégration d’AwardSpring à Azure AD, vous devez ajouter AwardSpring à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter AwardSpring à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **AwardSpring**, sélectionnez **AwardSpring** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![AwardSpring dans la liste des résultats](./media/awardspring-tutorial/tutorial_awardspring_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec AwardSpring avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur AwardSpring équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur AwardSpring associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec AwardSpring, vous devez suivre les opérations suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test AwardSpring](#create-an-awardspring-test-user)** pour avoir un équivalent de Britta Simon dans AwardSpring, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application AwardSpring.

**Pour configurer l’authentification unique Azure AD avec AwardSpring, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **AwardSpring**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/awardspring-tutorial/tutorial_awardspring_samlbase.png)

3. Dans la section **Domaine et URL AwardSpring**, suivez les étapes ci-dessous pour configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique Domaine et URL AwardSpring](./media/awardspring-tutorial/tutorial_awardspring_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.awardspring.com/SignIn/SamlMetaData`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.awardspring.com/SignIn/SamlAcs`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique Domaine et URL AwardSpring](./media/awardspring-tutorial/tutorial_awardspring_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.awardspring.com/`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique AwardSpring](mailto:support@awardspring.com). 

5. L’application AwardSpring attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configure Single Sign-On](./media/awardspring-tutorial/tutorial_awardSpring_attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | Prénom | user.givenname |
    | Nom | user.surname |
    | Email | user.mail |
    | Nom d’utilisateur | user.userprinicipalname |
    | StudentID | <ID_étudiant> |

    > [!NOTE]
    > L’attribut StudentID est mappé à l’ID d’étudiant réel qui doit être passé dans les revendications. Pour obtenir cette valeur, contactez l’[équipe du support client AwardSpring](mailto:support@awardspring.com).

    a. Supprimez les attributs existants et ajoutez de nouveaux attributs. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/awardspring-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/awardspring-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    d. Cliquez sur **OK**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/awardspring-tutorial/tutorial_awardspring_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/awardspring-tutorial/tutorial_general_400.png)
    
9. Pour configurer l’authentification unique du côté **AwardSpring**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe de support technique AwardSpring](mailto:support@awardspring.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/awardspring-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/awardspring-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/awardspring-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/awardspring-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-awardspring-test-user"></a>Créer un utilisateur de test AwardSpring

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans AwardSpring. AwardSpring prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore, un utilisateur est créé lors d’une tentative d’accès à AwardSpring.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support AwardSpring](maito:support@awardspring.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AwardSpring.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à AwardSpring, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **AwardSpring**.

    ![Le lien AwardSpring dans la liste des applications](./media/awardspring-tutorial/tutorial_awardspring_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette AwardSpring dans le volet d’accès, vous devez être connecté automatiquement à votre application AwardSpring.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/awardspring-tutorial/tutorial_general_01.png
[2]: ./media/awardspring-tutorial/tutorial_general_02.png
[3]: ./media/awardspring-tutorial/tutorial_general_03.png
[4]: ./media/awardspring-tutorial/tutorial_general_04.png

[100]: ./media/awardspring-tutorial/tutorial_general_100.png

[200]: ./media/awardspring-tutorial/tutorial_general_200.png
[201]: ./media/awardspring-tutorial/tutorial_general_201.png
[202]: ./media/awardspring-tutorial/tutorial_general_202.png
[203]: ./media/awardspring-tutorial/tutorial_general_203.png

