---
title: 'Tutoriel : Intégration d’Azure Active Directory à Nimblex | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Nimblex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.openlocfilehash: 63b70e38f4c4f804c9be520480c7562834d42598
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37870614"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Tutoriel : Intégration d’Azure Active Directory à Nimblex

Dans ce tutoriel, vous découvrez comment intégrer Nimblex à Azure Active Directory (Azure AD).

L’intégration de Nimblex à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Nimblex.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Nimblex (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Nimblex, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Nimblex avec authentification unique

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Nimblex à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-nimblex-from-the-gallery"></a>Ajout de Nimblex à partir de la galerie
Pour configurer l’intégration de Nimblex à Azure AD, vous devez ajouter Nimblex à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Nimblex à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Nimblex**, sélectionnez **Nimblex** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Nimblex dans la liste des résultats](./media/nimblex-tutorial/tutorial_nimblex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Nimblex au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Nimblex équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Nimblex associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Nimblex, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Nimblex](#create-a-nimblex-test-user)** pour avoir dans Nimblex un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Nimblex.

**Pour configurer l’authentification unique Azure AD avec Nimblex, effectuez les étapes suivantes :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Nimblex**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/nimblex-tutorial/tutorial_nimblex_samlbase.png)

3. Dans la section **Domaine et URL Nimblex**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Nimblex](./media/nimblex-tutorial/tutorial_nimblex_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YOUR APPLICATION PATH>/Login.aspx`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<YOUR APPLICATION PATH>/`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique de Nimblex](mailto:support@ebms.com.au).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/nimblex-tutorial/tutorial_nimblex_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/nimblex-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Nimblex**, cliquez sur **Configurer Nimblex** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Nimblex](./media/nimblex-tutorial/tutorial_nimblex_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à Nimblex comme Administrateur de la sécurité.

9. À droite de la page, cliquez sur le logo **Paramètres**.

    ![Paramètres de Nimblex](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

10. Dans la page **Panneau de configuration**, sous la section **Sécurité**, cliquez sur **Authentification unique**.

    ![Paramètres de Nimblex](./media/nimblex-tutorial/tutorial_nimblex_single.png)

11. Dans la page **Gérer l’authentification unique**, sélectionnez le nom de votre instance, puis cliquez sur **Modifier**.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

12. Dans la page **Modifier le fournisseur SSO**, effectuez les étapes suivantes :

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. Dans la zone de texte **Description**, tapez le nom de votre instance.

    b. Dans le Bloc-notes, ouvrez le certificat codé en base 64 téléchargé dans le portail Azure, copiez son contenu, puis collez-le dans la zone **Certificat**.

    c. Dans la zone de texte **URL SSO cible du fournisseur d’identité**, collez la valeur de **l’URL du service d’authentification unique SAML** copiée dans le portail Azure.

    d. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/nimblex-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/nimblex-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/nimblex-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/nimblex-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-nimblex-test-user"></a>Créer un utilisateur de test Nimblex

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Nimblex. Nimblex prend en charge le provisionnement juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé pendant la tentative d’accès à Nimblex, s’il n’existe pas déjà.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique du client Nimblex](mailto:support@ebms.com.au).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Nimblex.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à Nimblex, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Nimblex**.

    ![Lien Nimblex dans la liste des applications](./media/nimblex-tutorial/tutorial_nimblex_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Nimblex dans le volet d’accès, vous devez être connecté automatiquement à votre application Nimblex.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nimblex-tutorial/tutorial_general_01.png
[2]: ./media/nimblex-tutorial/tutorial_general_02.png
[3]: ./media/nimblex-tutorial/tutorial_general_03.png
[4]: ./media/nimblex-tutorial/tutorial_general_04.png

[100]: ./media/nimblex-tutorial/tutorial_general_100.png

[200]: ./media/nimblex-tutorial/tutorial_general_200.png
[201]: ./media/nimblex-tutorial/tutorial_general_201.png
[202]: ./media/nimblex-tutorial/tutorial_general_202.png
[203]: ./media/nimblex-tutorial/tutorial_general_203.png

