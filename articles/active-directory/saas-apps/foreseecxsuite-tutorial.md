---
title: 'Tutoriel : Intégration d’Azure Active Directory à ForeSee CX Suite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ForeSee CX Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: e27bc853db6236165d77f2fb3418bab1170400b2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227701"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Tutoriel : Intégration d’Azure Active Directory à ForeSee CX Suite

Dans ce tutoriel, vous allez apprendre à intégrer ForeSee CX Suite à Azure Active Directory (Azure AD).

L’intégration de ForeSee CX Suite à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ForeSee CX Suite.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ForeSee CX Suite (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ForeSee CX Suite, vous aurez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ForeSee CX Suite pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter ForeSee CX Suite à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Ajouter ForeSee CX Suite à partir de la galerie
Pour configurer l’intégration de ForeSee CX Suite à Azure AD, vous devez ajouter ForeSee CX Suite à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter ForeSee CX Suite à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **ForeSee CX Suite**, sélectionnez **ForeSee CX Suite** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![ForeSee CX Suite dans la liste des résultats](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ForeSee CX Suite pour un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir quel utilisateur ForeSee CX Suite correspond à l’utilisateur Azure AD. En d’autres termes, il est nécessaire d’établir une relation entre un utilisateur Azure AD et l’utilisateur ForeSee CX Suite associé.

Pour configurer et tester l’authentification unique Azure AD avec ForeSee CX Suite, suivez les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test ForeSee CX Suite](#create-a-foresee-cx-suite-test-user)** pour avoir un équivalent de Britta Simon dans ForeSee CX Suite, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le Portail Azure et configurer l’authentification unique dans votre application ForeSee CX Suite.

**Pour configurer l’authentification unique Azure AD avec ForeSee CX Suite, suivez les étapes ci-dessous :**

1. Sur la page d’intégration de l’application **ForeSee CX Suite** du Portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

3. Dans la section **Domaine et URL ForeSee CX Suite**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL ForeSee CX Suite](./media/foreseecxsuite-tutorial/upload.png)

    a. Cliquez sur **Charger un fichier de métadonnées**.

    ![Informations d’authentification unique dans Domaine et URL ForeSee CX Suite](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis sur **Charger**.

    c. Une fois le chargement du **fichier de métadonnées du fournisseur de services** terminé, la valeur **Identificateur** est renseignée automatiquement dans la zone de texte de la section **Domaine et URL ForeSee CX Suite**, comme indiqué ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL ForeSee CX Suite](./media/foreseecxsuite-tutorial/urlupload.png)

4. Si vous n’avez pas de **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL ForeSee CX Suite](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://cxsuite.foresee.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Mettez à jour cette valeur avec l’identificateur réel. Pour obtenir cette valeur, contactez [l’équipe de support client de ForeSee CX Suite](mailto:support@foresee.com).

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique côté **ForeSee CX Suite**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe de support de ForeSee CX Suite](mailto:support@foresee.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Créer un utilisateur de test ForeSee CX Suite

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans ForeSee CX Suite. Rapprochez-vous de [l’équipe de support de ForeSee CX Suite](mailto:support@foresee.com) pour ajouter les utilisateurs ou le domaine à mettre sur liste verte sur la plateforme ForeSee CX Suite. Si le domaine est ajouté par l’équipe, les utilisateurs sont automatiquement attribués à la plateforme ForeSee CX Suite. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ForeSee CX Suite.

![Attribuer le rôle utilisateur][200]

**Pour attribuer Britta Simon à ForeSee CX Suite, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **ForeSee CX Suite**.

    ![Lien ForeSee CX Suite dans la liste Applications](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette ForeSee CX Suite dans le volet d’accès, la connexion à votre application ForeSee CX Suite doit se faire automatiquement.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

