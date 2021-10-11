---
title: 'Tutoriel : Intégration de l’authentification unique Azure AD à Zero Networks'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zero Networks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 533e1dee16a303931987bc83f15f929d89d38b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669363"
---
# <a name="tutorial-azure-ad-sso-integration-with-zero-networks"></a>Tutoriel : Intégration de l’authentification unique Azure AD à Zero Networks

Dans ce tutoriel, vous allez apprendre à intégrer Zero Networks à Azure Active Directory (Azure AD). L’intégration de Zero Networks à Azure AD vous permet d’effectuer les actions suivantes :

* Contrôlez dans Azure AD qui a accès à Zero Networks.
* Permettez à vos utilisateurs de se connecter automatiquement à Zero Networks avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zero Networks pour lequel l’authentification unique (SSO, Single Sign-On) est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous configurez l’authentification unique Azure AD pour le portail d’administration et le portail d’accès de Zero Networks.

* Zero Networks prend en charge l’authentification unique à l’initiative du **fournisseur de services**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-zero-networks-from-the-gallery"></a>Ajout de Zero Networks à partir de la galerie

Pour configurer l’intégration de Zero Networks à Azure AD, vous devez ajouter Zero Networks à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure en utilisant un compte Microsoft professionnel.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zero Networks** dans la zone de recherche.
1. Sélectionnez **Zero Networks** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Zero Networks**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, procédez comme suit.

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://portal.zeronetworks.com/#/login`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Zero Networks**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

## <a name="configure-zero-networks-sso&quot;></a>Configuration de l’authentification unique Zero Networks

1. Connectez-vous au portail d’administration Zero Networks en tant qu’administrateur.

1. Accédez à **Paramètres** > **Fournisseurs d’identité**.

1. Cliquez sur **Microsoft Azure**, puis procédez comme suit.
    
    ![Capture d’écran montrant les paramètres de la configuration SSO.](./media/zero-networks-tutorial/settings.png &quot;Compte")

    1. Copiez la valeur du champ **Identifier(Entity ID)** , puis collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    1. Copiez la valeur de **Reply URL (Assertion Consumer Service URL)** (URL de réponse (URL Assertion Consumer Service)) et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** sur le portail Azure.

    1. Dans la zone de texte **URL de connexion**, collez la valeur **URL de connexion** que vous avez copiée sur le Portail Azure.

    1. Dans la zone de texte **URL de déconnexion**, collez la valeur **URL de déconnexion** que vous avez copiée sur le Portail Azure.

    1. Ouvrez le **Certificat (Base64)** que vous avez téléchargé sur le Portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Certificat (Base64)** .

    1. Cliquez sur **Save**.

## <a name="configure-user-assignment-requirement"></a>Configurer les conditions d’affectation d’utilisateurs

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Zero Networks**, recherchez la section **Gérer** et sélectionnez **Propriétés**.
1. Changez **Affectation utilisateur requise ?** en **Non**.

![Capture d’écran montrant Affectation utilisateur requise.](./media/zero-networks-tutorial/user-assignment.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

* Cliquez sur **Tester cette application** dans le portail Azure. L’URL de connexion à Zero Networks, qui permet d’amorcer le flux de connexion, s’ouvre. 

* Accédez directement à l’URL de connexion à Zero Networks pour lancer le flux de connexion.

* Vous pouvez utiliser Mes applications de Microsoft. Quand vous cliquez sur la vignette Zero Networks dans Mes applications, l’URL de connexion à Zero Networks s’ouvre. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](../user-help/my-apps-portal-end-user-access.md).
