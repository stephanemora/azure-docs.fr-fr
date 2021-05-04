---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Banyan Command Center | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Banyan Command Center.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: jeedes
ms.openlocfilehash: cf4da4d6b97e4c98be12d2a27d7fd8808b8b57d5
ms.sourcegitcommit: 12f15775e64e7a10a5daebcc52154370f3e6fa0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2021
ms.locfileid: "108001985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-banyan-command-center"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à Banyan Command Center

Dans ce tutoriel, vous apprenez à intégrer Banyan Command Center à Azure Active Directory (Azure AD). Quand vous intégrez Banyan Command Center à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Banyan Command Center.
* Permettre à vos utilisateurs de se connecter automatiquement à Banyan Command Center avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Banyan Command Center avec l’authentification unique activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Banyan Command Center prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* Banyan Command Center prend en charge le provisionnement d’utilisateurs **juste-à-temps**.


## <a name="adding-banyan-command-center-from-the-gallery"></a>Ajout de Banyan Command Center à partir de la galerie

Pour configurer l’intégration de Banyan Command Center à Azure AD, vous devez ajouter Banyan Command Center à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Banyan Command Center** dans la zone de recherche.
1. Sélectionnez **Banyan Command Center** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-banyan-command-center"></a>Configurer et tester l’authentification unique Azure AD pour Banyan Command Center

Configurez et testez l’authentification unique Azure AD dans Banyan Command Center pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Banyan Command Center.

Pour configurer et tester l’authentification unique Azure AD dans Banyan Command Center, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique dans Banyan Command Center](#configure-banyan-command-center-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test dans Banyan Command Center](#create-banyan-command-center-test-user)** pour avoir un équivalent de B.Simon dans Banyan Command Center lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **Banyan Command Center**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://net.banyanops.com/api/v1/sso?orgname=<YOUR_ORG_NAME>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support de Banyan Command Center](mailto:support@banyansecurity.io). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Banyan Command Center.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Banyan Command Center**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-banyan-command-center-sso"></a>Configurer l’authentification unique dans Banyan Command Center

1. Connectez-vous à votre site web Banyan Command Center en tant qu’administrateur.

1. Accédez à **Admin Settings (Paramètres d’administration) -> Admin Sign-on (Connexion de l’administrateur)** .

1. Effectuez les étapes suivantes dans la page **Sign-on Settings** (Paramètres de connexion).

    ![Capture d’écran des paramètres de connexion.](./media/banyan-command-center-tutorial/configuration.png)

    a. Pour **Sign-On Method** (Méthode de connexion) sélectionnez **Single Sign On - SAML 2.0** (Authentification unique - SAML 2.0) dans la liste déroulante.

    b. Copiez la valeur du champ **IDP Issuer** (émetteur du fournisseur d’identité), collez-la dans la zone de texte **Identificateur Azure AD** dans la section Configuration SAML de base du portail Azure.

    c. Collez la valeur de l’**URL des métadonnées de fédération d’application** dans la zone de texte **IDP Metadata URL** (URL des métadonnées du fournisseur d’identité).

    d. Cliquez sur le bouton **Update** (Mettre à jour).

### <a name="create-banyan-command-center-test-user"></a>Créer un utilisateur de test dans Banyan Command Center

Dans cette section, un utilisateur nommé Britta Simon est créé dans Banyan Command Center. Banyan Command Center prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’y a pas d’utilisateur dans Banyan Command Center, il est créé après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion de Banyan Command Center, où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion de Banyan Command Center pour lancer le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors connecté automatiquement à l’instance Banyan Command Center pour laquelle vous avez configuré l’authentification unique 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette Banyan Command Center dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion. S’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance Banyan Command Center pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré Banyan Command Center, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


