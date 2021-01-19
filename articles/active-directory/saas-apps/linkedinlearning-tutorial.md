---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à LinkedIn Learning | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Learning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 33ac21eb87b57487277e598e899061e8757e82fc
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050406"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à LinkedIn Learning

Dans ce tutoriel, vous allez apprendre à intégrer LinkedIn Learning à Azure AD (Azure Active Directory). Quand vous intégrez LinkedIn Learning à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à LinkedIn Learning.
* Permettre à vos utilisateurs de se connecter automatiquement à LinkedIn Learning avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement LinkedIn Learning pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* LinkedIn Learning prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**
* LinkedIn Learning prend en charge l’attribution d’utilisateurs **Juste-à-temps**


## <a name="adding-linkedin-learning-from-the-gallery"></a>Ajout de LinkedIn Learning à partir de la galerie

Pour configurer l’intégration de LinkedIn Learning à Azure AD, vous devez ajouter LinkedIn Learning à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **LinkedIn Learning** dans la zone de recherche.
1. Sélectionnez **LinkedIn Learning** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Configurer et tester l’authentification unique Azure AD pour LinkedIn Learning

Configurez et testez l’authentification unique Azure AD avec LinkedIn Learning pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur LinkedIn Learning associé.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Learning, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique LinkedIn Learning](#configure-linkedin-learning-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test LinkedIn Learning](#create-linkedin-learning-test-user)** pour avoir un équivalent de B.Simon dans LinkedIn Learning lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **LinkedIn Learning**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

     a. Dans la zone de texte **Identificateur**, saisissez **l’ID d’entité** copié à partir du portail LinkedIn. 

    b. Dans la zone de texte **URL de réponse**, saisissez **l’URL ACS** copiée à partir du portail LinkedIn.

    c. Si vous souhaitez configurer l’application en mode **initiée par le fournisseur de services**, cliquez sur l’option **Définir des URL supplémentaires** dans la section **Configuration SAML de base** où vous indiquerez votre URL de connexion. Pour créer votre URL de connexion, copiez l’**URL ACS** et remplacez /saml/ par /login/. Une fois cette opération effectuée, l’URL de connexion doit avoir le format suivant :

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informations d’authentification unique dans Domaine et URL LinkedIn Learning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous mettrez à jour ces valeurs avec l’identificateur et l’URL de réponse réels, tel qu’expliqué dans la section **Configurer l’authentification unique LinkedIn Learning** de ce tutoriel.

1. L’application LinkedIn Learning attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application LinkedIn Learning s’attend à ce que **nameidentifier** soit mappé avec **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![image](common/edit-attribute.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer LinkedIn Learning**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à LinkedIn Learning.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **LinkedIn Learning**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-linkedin-learning-sso"></a>Configurer l’authentification unique LinkedIn Learning

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre client LinkedIn Learning en tant qu’administrateur.

2. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. Sélectionnez aussi **Apprentissage - Par défaut** dans la liste déroulante.

    ![Capture d’écran montrant les paramètres globaux pour lesquels vous pouvez sélectionner l’option Default.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Cliquez sur  **OU cliquez ici pour charger et copier les champs individuels du formulaire**, puis copiez l’**ID d’entité** et l’**URL ACS (Assertion Consumer Service)** et copiez-les dans la section **Configuration SAML de base** du portail Azure.

    ![Capture d’écran montrant l’authentification unique pour laquelle vous pouvez entrer les valeurs décrites.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Chargez le fichier XML que vous avez téléchargé à partir du portail Azure en cliquant sur l’option **Télécharger fichier XML**.

    ![Capture d’écran montrant la section Configure the LinkedIn service provider SSO settings, dans laquelle vous pouvez charger un fichier XML.](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passe de **Non connecté** à **Connecté**

    ![Capture d’écran montrant l’authentification unique pour laquelle vous pouvez activer l’option Authenticate users with SSO.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Créer un utilisateur de test LinkedIn Learning

L’application LinkedIn Learning prend en charge l’attribution d’utilisateurs juste à temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. Dans la page de paramètres administrateur du portail LinkedIn Learning, activez le commutateur **Affecter automatiquement les licences** pour l’attribution juste à temps. Cela affectera également une licence à l’utilisateur.

   ![Création d’un utilisateur de test Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion LinkedIn Learning où vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion LinkedIn Learning et démarrez le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans le portail Azure : vous devez être connecté automatiquement à l’instance de LinkedIn Learning pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Si, quand vous cliquez sur la vignette LinkedIn Learning dans Mes applications, le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; s’il s’agit du mode Fournisseur d’identité, vous êtes automatiquement connecté à l’instance LinkedIn Learning pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré LinkedIn Learning, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
