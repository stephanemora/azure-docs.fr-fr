---
title: 'Tutoriel : Intégration d’Azure Active Directory à ArcGIS Enterprise | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ca5bf7ae49cf120c0566419ccadeff92433c6467
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819325"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutoriel : Intégration d’Azure Active Directory à ArcGIS Enterprise

Dans ce tutoriel, vous allez apprendre à intégrer ArcGIS Enterprise à Azure Active Directory (Azure AD).

L’intégration du logiciel ArcGIS Enterprise à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous contrôlez qui a accès à ArcGIS Enterprise.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à ArcGIS Enterprise (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ArcGIS Enterprise, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ArcGIS Enterprise pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’ArcGIS Enterprise à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Ajout d’ArcGIS Enterprise à partir de la galerie

Pour configurer l’intégration d’ArcGIS Enterprise à Azure AD, vous devez ajouter ArcGIS Enterprise à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter ArcGIS Enterprise à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **ArcGIS Enterprise**, sélectionnez **ArcGIS Enterprise** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![ArcGIS Enterprise dans la liste des résultats](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD dans ArcGIS Enterprise avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ArcGIS Enterprise équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur ArcGIS Enterprise associé.

Pour configurer et tester l’authentification unique Azure AD avec ArcGIS Enterprise, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test ArcGIS Enterprise](#create-an-arcgis-enterprise-test-user)** pour obtenir un équivalent de Britta Simon dans ArcGIS Enterprise, qui soit lié à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ArcGIS Enterprise.

**Pour configurer l’authentification unique Azure AD avec ArcGIS Enterprise, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **ArcGIS Enterprise**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. Dans la section **Domaines et URL ArcGIS Enterprise**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique de domaine et d’URL ArcGIS Enterprise](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `<EXTERNAL_DNS_NAME>.portal`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique de domaine et d’URL ArcGIS Enterprise](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique ArcGIS Enterprise](mailto:nshampur@esri.com). La valeur de l’identificateur peut être obtenue dans la section **Set Identity Provider** (Configurer le fournisseur d’identité), ce qui est expliqué plus loin dans ce tutoriel.

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous en tant qu’administrateur à votre site d’entreprise ArcGIS Enterprise.

8. Sélectionnez **Organization > EDIT SETTINGS** (Organisation > Modifier les paramètres).

    ![Configuration d’ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

9. Sélectionnez l’onglet **Sécurité**.

    ![Configuration d’ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

10. Faites défiler jusqu’à la section **Enterprise Logins via SAML** (Connexions d’entreprise via SAML), puis sélectionnez **SET ENTERPRISE LOGIN** (Définir les connexions d’entreprise).

    ![Configuration d’ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

11. Dans la section **Set Identity Provider** (Configurer le fournisseur d’identité), effectuez les étapes suivantes :

    ![Configuration d’ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. Fournissez un nom tel que **Azure Active Directory Test** dans la zone de texte **Name**.

    b. Dans la zone de texte **URL**, collez la valeur du champ **URL des métadonnées de fédération d’application** du portail Azure.

    c. Cliquez sur **Show advanced settings** (Afficher les paramètres avancés). Copiez la valeur qui se trouve sous **Entity ID** (ID d’entité), puis collez-la dans la zone de texte **Identificateur** de la section **Domaines et URL ArcGIS Enterprise** du portail Azure.
    
    ![Configuration d’ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Cliquez sur **UPDATE IDENTITY PROVIDER** (Mettre à jour le fournisseur d’identité).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-an-arcgis-enterprise-test-user"></a>Créer un utilisateur de test ArcGIS Enterprise

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ArcGIS Enterprise. ArcGIS Enterprise prend en charge le provisionnement juste-à-temps, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un utilisateur est créé lors d’une tentative d’accès à ArcGIS Enterprise.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique ArcGIS Enterprise](mailto:nshampur@esri.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ArcGIS Enterprise.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à ArcGIS Enterprise, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **ArcGIS Enterprise**.

    ![Lien ArcGIS Enterprise dans la liste des applications](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette ArcGIS Enterprise dans le volet d’accès, vous êtes connecté automatiquement à votre application ArcGIS Enterprise.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png