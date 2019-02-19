---
title: 'Tutoriel : Intégration d’Azure Active Directory à iPass SmartConnect | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a68a208496904fcc8bfe13a227c61bf313214f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198165"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Tutoriel : Intégration d’Azure Active Directory à iPass SmartConnect

Dans ce tutoriel, vous allez apprendre à intégrer iPass SmartConnect à Azure Active Directory (Azure AD).

L’intégration de iPass SmartConnect à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à iPass SmartConnect.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à iPass SmartConnect (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration de iPass SmartConnect à Azure AD, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement iPass SmartConnect pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter iPass SmartConnect à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Ajouter iPass SmartConnect à partir de la galerie
Pour configurer l’intégration de iPass SmartConnect à Azure AD, vous devez ajouter iPass SmartConnect, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter iPass SmartConnect à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **iPass SmartConnect**, sélectionnez **iPass SmartConnect** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![iPass SmartConnect dans la liste des résultats](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec iPass SmartConnect avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur iPass SmartConnect équivalent dans Azure AD. En d’autres termes, une relation de lien entre un utilisateur Azure AD et un utilisateur iPass SmartConnect associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec iPass SmartConnect, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test iPass SmartConnect](#create-an-ipass-smartconnect-test-user)** pour avoir un équivalent de Britta Simon dans iPass SmartConnect qui soit lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application iPass SmartConnect.

**Pour configurer l’authentification unique Azure AD avec iPass SmartConnect, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **iPass SmartConnect**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. Dans la section **iPass SmartConnect Domain and URLs** (Domaines et URL iPass SmartConnect), si vous souhaitez configurer l’application en mode initié par **IDP**, vous n’avez aucune opération à effectuer.

    ![Informations d’authentification unique dans Domaine et URL iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Si vous souhaitez configurer l’application en mode démarré par le **fournisseur de services**, cochez Afficher les paramètres d’URL avancés, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    Dans la zone de texte URL d’authentification, tapez l’URL : `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. L’application iPass SmartConnect attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :

    ![Configurer l'authentification unique](./media/ipasssmartconnect-tutorial/attribute.png)

1. Dans la section **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** pour développer les attributs. Dans chacun des attributs affichés, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut | Valeur d'espace de noms|
    | ---------------| --------------- |----------------|
    | firstName | user.givenname |   |
    | lastName | user.surname | |
    | email | user.userprincipalname | |
    | username | user.userprincipalname | |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Pour cette ligne, conservez la valeur de l’espace de noms vide.

    e. Cliquez sur **OK**.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Pour configurer l’authentification unique du côté **iPass SmartConnect**, vous devez envoyer le **XML de métadonnées** téléchargé et votre **Nom de domaine** à [l’équipe de support technique de iPass SmartConnect](mailto:help@ipass.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Créer un utilisateur de test iPass SmartConnect

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans iPass SmartConnect. Rapprochez-vous de  [l’équipe de support de iPass SmartConnect](mailto:help@ipass.com)  pour ajouter les utilisateurs ou le domaine à mettre sur liste verte sur la plateforme iPass SmartConnect. Si le domaine est ajouté par l’équipe, les utilisateurs sont automatiquement provisionnés dans la plateforme iPass SmartConnect. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à iPass SmartConnect.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à iPass SmartConnect, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

1. Dans la liste des applications, sélectionnez **iPass SmartConnect**.

    ![Lien iPass SmartConnect dans la liste des applications](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

**Pour tester l’application dans le flux initié par SP, procédez comme suit :**

a. Téléchargez le client iPass SmartConnect Windows [ici](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![Lien iPass SmartConnect dans la liste des applications](./media/ipasssmartconnect-tutorial/testing3.png)

b. Installez le client et lancez-le.

c. Cliquez sur **Prise en main**.

![Lien iPass SmartConnect dans la liste des applications](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Entrez le nom d’utilisateur Azure et le domaine. Cliquez sur **Continuer**. Vous êtes redirigé vers la page de connexion à Azure.

![Lien iPass SmartConnect dans la liste des applications](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Une fois l’authentification effectuée, l’activation du client démarre. Le client est activé.

**Pour tester l’application dans le flux initié par IdP, procédez comme suit :**

a. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).

b. Cliquez sur l’application iPass SmartConnect.

c. La page SSA s’affiche, cliquez sur **Télécharger l’application pour Windows** pour installer le client iPass SmartConnect.

![Lien iPass SmartConnect dans la liste des applications](./media/ipasssmartconnect-tutorial/testing4.png)

d. Après l’installation, lors du premier démarrage du client, l’activation démarre automatiquement une fois les conditions d’utilisation acceptées.

e. Si l’activation ne démarre pas, cliquez sur le bouton Activer sur la page SSA pour lancer l’activation.

f. Le client est activé.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

