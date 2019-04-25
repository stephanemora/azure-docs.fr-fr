---
title: "Didacticiel : Intégration d'Azure Active Directory à SecureW2 JoinNow Connector | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SecureW2 JoinNow Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b367befb90ec28ece963d67b479749e1c8ad363
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60339831"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>Didacticiel : Intégration d'Azure Active Directory à SecureW2 JoinNow Connector

Dans ce tutoriel, vous allez apprendre à intégrer SecureW2 JoinNow Connector à Azure Active Directory (Azure AD).

L’intégration de SecureW2 JoinNow Connector à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SecureW2 JoinNow Connector.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SecureW2 JoinNow Connector (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à SecureW2 JoinNow Connector, vous aurez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à SecureW2 JoinNow Connector pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SecureW2 JoinNow Connector à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Ajout de SecureW2 JoinNow Connector à partir de la galerie
Pour configurer l’intégration de SecureW2 JoinNow Connector à Azure AD, vous devez ajouter SecureW2 JoinNow Connector à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter SecureW2 JoinNow Connector à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SecureW2 JoinNow Connector**, sélectionnez **SecureW2 JoinNow Connector** dans le volet de résultats, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![SecureW2 JoinNow Connector dans la liste des résultats](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SecureW2 JoinNow Connector, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SecureW2 JoinNow Connector pour son utilisateur équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur SecureW2 JoinNow Connector qui lui est associé.

Pour configurer et tester l’authentification unique Azure AD avec SecureW2 JoinNow Connector, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test SecureW2 JoinNow Connector](#create-a-securew2-joinnow-connector-test-user)** pour avoir un équivalent de Britta Simon dans SecureW2 JoinNow Connector lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SecureW2 JoinNow Connector.

**Pour configurer l’authentification unique Azure AD avec SecureW2 JoinNow Connector, effectuez les étapes suivantes :**

1. Dans la page d’intégration de l’application **SecureW2 JoinNow Connector** du portail Azure, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. Dans la section **Domaine et URL SecureW2 JoinNow Connector**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL SecureW2 JoinNow Connector](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique SecureW2 JoinNow Connector](mailto:support@securew2.com). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique du côté de **SecureW2 JoinNow Connector**, vous devez envoyer les **métadonnées XML** téléchargées à [l’équipe du support technique de SecureW2 JoinNow Connector](mailto:support@securew2.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>Créer un utilisateur de test SecureW2 JoinNow Connector

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SecureW2 JoinNow Connector. Travaillez en collaboration avec l' [équipe du support technique de SecureW2 JoinNow Connector](mailto:support@securew2.com) pour ajouter des utilisateurs à la plateforme SecureW2 JoinNow Connector. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SecureW2 JoinNow Connector.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à SecureW2 JoinNow Connector, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SecureW2 JoinNow Connector**.

    ![Lien SecureW2 JoinNow Connector dans la liste des applications](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

**Pour tester l’application, effectuez les étapes suivantes :** 

a. Ouvrez le client SecureW2 JoinNow Connector, sélectionnez l’appareil souhaité dans la liste, puis cliquez sur le bouton **Connexion**.

b. Le navigateur par défaut doit s’ouvrir et vous devez être redirigé vers le portail Azure pour l’authentification.

c. Si l’authentification réussit, vous êtes redirigé vers la page d’accueil initiale de SecureW2 JoinNow Connector.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

