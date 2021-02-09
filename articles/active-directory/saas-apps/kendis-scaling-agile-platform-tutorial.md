---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Kendis-Scaling Agile Platform | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kendis-Scaling Agile Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: e02ff4926897fafc72e1a5081366faad5d2f03ba
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509608"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis-scaling-agile-platform"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Kendis-Scaling Agile Platform

Dans ce tutoriel, vous allez découvrir comment intégrer Kendis-Scaling Agile Platform à Azure Active Directory (Azure AD). Quand vous intégrez Kendis-Scaling Agile Platform à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Kendis-Scaling Agile Platform.
* Permettre à vos utilisateurs de se connecter automatiquement à Kendis-Scaling Agile Platform avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Kendis-Scaling Agile Platform pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Kendis-Scaling Agile Platform prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**
* Kendis-Scaling Agile Platform prend en charge le provisionnement d’utilisateurs **juste-à-temps**


## <a name="adding-kendis-scaling-agile-platform-from-the-gallery"></a>Ajout de Kendis-Scaling Agile Platform à partir de la galerie

Pour configurer l’intégration de Kendis-Scaling Agile Platform à Azure AD, vous devez ajouter Kendis-Scaling Agile Platform à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Kendis-Scaling Agile Platform** dans la zone de recherche.
1. Sélectionnez **Kendis-Scaling Agile Platform** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-kendis-scaling-agile-platform"></a>Configurer et tester l’authentification unique Azure AD pour Kendis-Scaling Agile Platform

Configurez et testez l’authentification unique Azure AD avec Kendis-Scaling Agile Platform pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Kendis-Scaling Agile Platform associé.

Pour configurer et tester l’authentification unique Azure AD avec Kendis-Scaling Agile Platform, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Kendis-Scaling Agile Platform](#configure-kendis-scaling-agile-platform-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Kendis-Scaling Agile Platform](#create-kendis-scaling-agile-platform-test-user)** pour avoir un équivalent de B.Simon dans Kendis-Scaling Agile Platform lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **Kendis-Scaling Agile Platform**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.kendis.io`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Kendis-Scaling Agile Platform](mailto:support@kendis.io). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Kendis-Scaling Agile Platform**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kendis-Scaling Agile Platform.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Kendis-Scaling Agile Platform**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-kendis-scaling-agile-platform-sso"></a>Configuration de l’authentification unique Kendis-Scaling Agile Platform

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Kendis-Scaling Agile Platform en tant qu’administrateur.

1. Accédez à **Settings > SAML Configurations**.

    ![paramètres des configurations SAML](./media/kendis-scaling-agile-platform-tutorial/settings.png)

1. Cliquez sur le bouton **Edit** au bas de la page et effectuez les étapes suivantes.

    ![Configurations SAML](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Copiez la valeur du champ **Callback URL**, puis collez-la dans la zone de texte **URL de réponse** de la section Configuration SAML de base du portail Azure.

    b. Dans la zone de texte **Identity Provider Single Sign-On URL**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c.  Dans la zone de texte **Identity Provider Issuer**, collez la valeur de l’**identificateur Azure AD (ID d’entité)** que vous avez copiée à partir du portail Azure.

    d. Ouvrez le **Certificat (en base64)** téléchargé à partir du portail Azure dans le Bloc-notes, puis collez le contenu dans la zone de texte **X.509 Certificate**.

    e. **Sélectionnez le groupe par défaut** dans la liste des options. 

    f. Cliquez sur **Enregistrer**.

### <a name="create-kendis-scaling-agile-platform-test-user"></a>Créer un utilisateur de test Kendis-Scaling Agile Platform

Dans cette section, un utilisateur appelé Britta Simon est créé dans Kendis-Scaling Agile Platform. Kendis-Scaling Agile Platform prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Kendis-Scaling Agile Platform, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Cette opération redirige vers l’URL de connexion Kendis-Scaling Agile Platform où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion Kendis-Scaling Agile Platform pour lancer le flux de connexion à partir de là.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance de Kendis-Scaling Agile Platform pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette Kendis-Scaling Agile Platform dans le volet d’accès, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance de Kendis-Scaling Agile Platform pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Kendis-Scaling Agile Platform, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


