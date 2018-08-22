---
title: 'Didacticiel : Intégration d’Azure Active Directory à Dovetale | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dovetale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81da50c3-df94-458a-8b6a-a30827ee6358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: dd2aa699c22518ebbe7f29ba8dfd296da7385476
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161696"
---
# <a name="tutorial-azure-active-directory-integration-with-dovetale"></a>Didacticiel : Intégration d’Azure Active Directory avec Dovetale

Dans ce didacticiel, vous allez apprendre à intégrer Dovetale à Azure Active Directory (Azure AD).

L’intégration de Dovetale à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Dovetale.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Dovetale (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Dovetale, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Dovetale pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Dovetale depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-dovetale-from-the-gallery"></a>Ajout de Dovetale depuis la galerie

Pour configurer l’intégration de Dovetale à Azure AD, vous devez ajouter Dovetale à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Dovetale à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Dovetale**, sélectionnez **Dovetale** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Dovetale dans la liste des résultats](./media/dovetale-tutorial/tutorial_dovetale_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Dovetale avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Dovetale équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Dovetale associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Dovetale, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Dovetale](#create-a-dovetale-test-user)** pour avoir un équivalent de Britta Simon dans Dovetale lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Dovetale.

**Pour configurer l’authentification unique Azure AD avec Dovetale, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Dovetale**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/dovetale-tutorial/tutorial_dovetale_samlbase.png)

3. Dans la section **Domaine et URL Dovetale**, si vous souhaitez configurer l’application en mode initialisé **IDP**, l’utilisateur n’a rien à faire car l’application est déjà pré-intégrée avec Azure :

    ![Informations d’authentification unique dans Domaine et URL Dovetale](./media/dovetale-tutorial/tutorial_dovetale_url1.png)

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Dovetale](./media/dovetale-tutorial/tutorial_dovetale_url2.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `<COMPANYNAME>.dovetale.com`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Contactez [l’équipe de support technique Dovetale](mailto:support@dovetale.com) pour obtenir cette valeur.

5. L’application Dovetale attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configurer l'authentification unique](./media/dovetale-tutorial/tutorial_attribute.png)

6. Dans la section **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** pour développer les attributs. Dans chacun des attributs affichés, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |
    | email | user.mail |
    | first_name | user.givenname |
    | Nom | user.userprincipalname |
    | last_name | user.surname |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/dovetale-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/dovetale-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Nom**, indiquez le **nom d’attribut** pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **ESPACE DE NOMS** vide.

    e. Cliquez sur **OK**.

7. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/dovetale-tutorial/tutorial_dovetale_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/dovetale-tutorial/tutorial_general_400.png)

9. Dans la section **Configuration de Dovetale**, cliquez sur **Configurer Dovetale** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Dovetale](./media/dovetale-tutorial/tutorial_dovetale_configure.png)

10. Pour configurer l’authentification unique côté **Dovetale**, vous devez envoyer **l’URL des métadonnées de fédération de l’application, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à [l’équipe du support technique Dovetale](mailto:support@dovetale.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/dovetale-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/dovetale-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/dovetale-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/dovetale-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-dovetale-test-user"></a>Créer un utilisateur de test Dovetale

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Dovetale. Dovetale prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Dovetale s’il n’existe pas déjà.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Dovetale](mailto:support@dovetale.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Dovetale.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Dovetale, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Dovetale**.

    ![Lien Dovetale dans la liste des applications](./media/dovetale-tutorial/tutorial_dovetale_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Dovetale dans le volet d’accès, vous devez vous connecter automatiquement à votre application Dovetale.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dovetale-tutorial/tutorial_general_01.png
[2]: ./media/dovetale-tutorial/tutorial_general_02.png
[3]: ./media/dovetale-tutorial/tutorial_general_03.png
[4]: ./media/dovetale-tutorial/tutorial_general_04.png

[100]: ./media/dovetale-tutorial/tutorial_general_100.png

[200]: ./media/dovetale-tutorial/tutorial_general_200.png
[201]: ./media/dovetale-tutorial/tutorial_general_201.png
[202]: ./media/dovetale-tutorial/tutorial_general_202.png
[203]: ./media/dovetale-tutorial/tutorial_general_203.png