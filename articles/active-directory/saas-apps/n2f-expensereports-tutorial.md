---
title: "Didacticiel : Intégration d'Azure Active Directory à N2F - Note de frais | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et N2F - Note de frais.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3166a4e19bc137d57b97dbb516e7037228e1c020
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188815"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Tutoriel : Intégration d'Azure Active Directory à N2F - Note de frais

Dans le cadre de ce didacticiel, vous allez apprendre à intégrer N2F - Note de frais à Azure Active Directory (Azure AD).

L’intégration de N2F - Note de frais à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à N2F - Note de frais.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à N2F - Note de frais (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à N2F - Note de frais, vous devez disposer des éléments suivants :

- Un abonnement Azure AD
- Un abonnement N2F - Note de frais pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de N2F - Note de frais à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Ajout de N2F - Note de frais à partir de la galerie

Pour configurer l’intégration de N2F - Note de frais à Azure AD, vous devez ajouter N2F - Note de frais à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter N2F - Note de frais à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **N2F - Note de frais**, sélectionnez **N2F - Note de frais** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![N2F - Note de frais dans la liste des résultats](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec N2F - Note de frais pour un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur N2F - Note de frais équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur N2F - Note de frais associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec N2F - Note de frais, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **Créer un utilisateur de test N2F - Note de frais** pour disposer dans N2F - Note de frais d'un équivalent de Britta Simon qui soit lié à la représentation Azure AD de l'utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application N2F - Note de frais.

**Pour configurer l’authentification unique Azure AD avec N2F - Note de frais, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **N2F - Note de frais**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. Dans la section **Domaine et URL N2F - Note de frais**, si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité**, l’utilisateur n’a rien à faire, car l’application est déjà pré-intégrée à Azure.

    ![Informations d’authentification unique dans Domaine et URL N2F - Note de frais](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL N2F - Note de frais](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://www.n2f.com/app/`

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de N2F - Note de frais**, cliquez sur **Configurer N2F - Note de frais** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. Dans une autre fenêtre de navigateur web, connectez-vous au site d’entreprise N2F - Note de frais en tant qu’administrateur.

9. Cliquez sur **Paramètres**, puis sélectionnez **Paramètres avancés** dans la liste déroulante.

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/configure1.png)

10. Sélectionnez l’onglet **Paramètres du compte**.

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/configure2.png)

11. Sélectionnez **Authentification**, puis sélectionnez l’onglet **+ Add an authentication method** (Ajouter une méthode d’authentification).

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/configure3.png)

12. Sélectionnez **SAML Microsoft Office 365** comme méthode d’authentification.

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/configure4.png)

13. Dans la section **Méthode d’authentification** , procédez comme suit :

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/configure5.png)

    a. Dans la zone de texte **ID d’entité**, collez la valeur **ID d’entité SAML** que vous avez copiée à partir du Portail Azure.

    b. Dans la zone de texte **URL de métadonnées**, collez la valeur **URL des métadonnées de fédération d’application** que vous avez copiée à partir du Portail Azure.

    c. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-n2f---expense-reports-test-user"></a>Créer un utilisateur de test N2F - Note de frais

Pour permettre aux utilisateurs Azure AD de se connecter à N2F - Note de frais, vous devez les approvisionner dans N2F - Note de frais. Dans le cas de N2F - Note de frais, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise N2F - Note de frais en tant qu’administrateur.

2. Cliquez sur **Paramètres**, puis sélectionnez **Paramètres avancés** dans la liste déroulante.

   ![Ajout d’un utilisateur N2F - Note de frais](./media/n2f-expensereports-tutorial/configure1.png)

3. Sélectionnez l’onglet **Utilisateurs** dans le volet de navigation de gauche.

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/user1.png)

4. Sélectionnez l’onglet **+ Nouvel utilisateur**.

   ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/user2.png)

5. Dans la section **Utilisateur**, procédez comme suit :

    ![Configuration de N2F - Note de frais](./media/n2f-expensereports-tutorial/user3.png)

    a. Dans la zone de texte **Email address** (Adresse e-mail), entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.

    b. Dans la zone de texte **First name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la zone de texte **Nom**, entrez le nom d’un utilisateur, par exemple **Britta Simon**.

    d. Choisissez **Role, Direct manager (N+1)** (Rôle, Supérieur hiérarchique [N+1]) et **Division** (Division) conformément aux exigences de votre organisation.

    e. Cliquez sur **Validate and send invitation** (Valider et envoyer une invitation).

    > [!NOTE]
    > Si vous rencontrez des problèmes lors de l’ajout de l’utilisateur, contactez [l’équipe du support technique de N2F - Note de frais](mailto:support@n2f.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à N2F - Note de frais.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à N2F - Note de frais, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **N2F - Note de frais**.

    ![Lien N2F - Note de frais dans la liste des applications](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette N2F - Note de frais dans le volet d’accès, vous devez être connecté automatiquement à votre application N2F - Note de frais.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

