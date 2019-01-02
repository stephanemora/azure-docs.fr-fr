---
title: 'Didacticiel : Intégration d’Azure Active Directory avec ProMaster (d’Inlogik) | Microsoft Docs'
description: Découvrez comment configurer une authentification unique entre Azure Active Directory et ProMaster (d’Inlogik).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 348dbd37-dc4f-49df-bb90-53d249d456b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeedes
ms.openlocfilehash: 871abffc07ee29df388205395110e89a85dd7344
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017652"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Didacticiel : Intégration d’Azure Active Directory avec ProMaster (d’Inlogik)

Ce didacticiel montre comment intégrer ProMaster (d’Inlogik) avec Azure Active Directory (Azure AD).

L’intégration de ProMaster (d’Inlogik) avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ProMaster (d’Inlogik).
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à ProMaster (d’Inlogik) à l’aide d’une authentification unique avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec ProMaster (d’Inlogik), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ProMaster (d’Inlogik) pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ProMaster (d’Inlogik) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Ajout de ProMaster (d’Inlogik) à partir de la galerie

Pour configurer l’intégration de ProMaster (d’Inlogik) dans Azure AD, vous devez ajouter ProMaster (d’Inlogik) à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ProMaster (d’Inlogik) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Tapez **ProMaster (d’Inlogik)** dans la zone de recherche, sélectionnez **ProMaster (d’Inlogik)** dans le volet des résultats, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![ProMaster (par Inlogik) dans la liste des résultats](./media/promaster-tutorial/tutorial_promaster_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ProMaster (d’Inlogik) en vous basant sur un utilisateur test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ProMaster (d’Inlogik) équivalent à un utilisateur dans Azure AD. En d’autres termes, une relation de lien entre un utilisateur Azure AD et un utilisateur ProMaster (d’Inlogik) associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec ProMaster (d’Inlogik), vous devez accomplir les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test ProMaster (d’Inlogik)](#create-a-promaster-by-inlogik-test-user)** pour avoir un équivalent de Britta Simon dans ProMaster (d’Inlogik) lié à la représentation d’utilisateur Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ProMaster (d’Inlogik).

**Pour configurer l’authentification unique Azure AD avec ProMaster (d’Inlogik), procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **ProMaster (d’Inlogik)**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/promaster-tutorial/tutorial_promaster_samlbase.png)

3. Dans la section **Domaines et URL de ProMaster (d’Inlogik)**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique de domaine et d’URL de ProMaster (d’Inlogik)](./media/promaster-tutorial/tutorial_promaster_url1.png)

    a. Dans la zone de texte **Identificateur**, utilisez l’un des modèles d’URL suivants :

    | |
    | - |-|
    |  `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    b. Dans la zone de texte **URL de réponse**, utilisez l’un des modèles d’URL suivants :

    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique de domaine et d’URL de ProMaster (d’Inlogik)](./media/promaster-tutorial/tutorial_promaster_url2.png)

    Dans la zone de texte **URL d’authentification**, utilisez l’un des modèles d’URL suivants :

    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs `|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique ProMaster (d’Inlogik)](mailto:michael.boldiston@inlogik.com).

5. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/promaster-tutorial/tutorial_promaster_certificate.png)

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/promaster-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique côté **ProMaster (d’Inlogik)**, vous devez envoyer **l’URL des métadonnées de fédération de l’application** à [l’équipe du support technique ProMaster (d’Inlogik)](mailto:michael.boldiston@inlogik.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/promaster-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/promaster-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/promaster-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/promaster-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-promaster-by-inlogik-test-user"></a>Créer un utilisateur de test de ProMaster (d’Inlogik)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans ProMaster (d’Inlogik). Collaborez avec l’ [équipe de support de ProMaster (par Inlogik)](mailto:michael.boldiston@inlogik.com)  pour ajouter les utilisateurs à la plateforme ProMaster (par Inlogik). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ProMaster (d’Inlogik).

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à ProMaster (d’Inlogik), procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste d’applications, sélectionnez **ProMaster (d’Inlogik)**.

    ![Lien ProMaster (d’Inlogik) dans la liste Applications](./media/promaster-tutorial/tutorial_promaster_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ProMaster (d’Inlogik) dans le volet d’accès, vous êtes connecté automatiquement à votre application ProMaster (d’Inlogik).
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promaster-tutorial/tutorial_general_01.png
[2]: ./media/promaster-tutorial/tutorial_general_02.png
[3]: ./media/promaster-tutorial/tutorial_general_03.png
[4]: ./media/promaster-tutorial/tutorial_general_04.png

[100]: ./media/promaster-tutorial/tutorial_general_100.png

[200]: ./media/promaster-tutorial/tutorial_general_200.png
[201]: ./media/promaster-tutorial/tutorial_general_201.png
[202]: ./media/promaster-tutorial/tutorial_general_202.png
[203]: ./media/promaster-tutorial/tutorial_general_203.png

