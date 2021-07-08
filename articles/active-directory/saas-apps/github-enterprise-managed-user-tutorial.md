---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory avec GitHub Enterprise Managed User | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et GitHub Enterprise Managed User.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: ffdd6c30e279cc5df7f97e5ab5bb77a87c18dd8b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110480495"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory avec GitHub Enterprise Managed User

Ce tutoriel explique comment intégrer GitHub Enterprise Managed User (EMU) à Azure Active Directory (Azure AD). Lorsque vous intégrez GitHub Enterprise Managed User avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à GitHub Enterprise Managed User.
* Permettre à vos utilisateurs de se connecter automatiquement à GitHub Enterprise Managed User avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement GitHub Enterprise Managed User pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* GitHub Enterprise Managed User prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**.
* GitHub Enterprise Managed User requiert l'[approvisionnement **automatique** d'utilisateurs](./github-enterprise-managed-user-provisioning-tutorial.md).

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>Ajout de GitHub Enterprise Managed User à partir de la galerie

Pour configurer l’intégration de GitHub Enterprise Managed User dans Azure AD, vous devez ajouter GitHub Enterprise Managed User à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **GitHub Enterprise Managed User** dans la zone de recherche.
1. Sélectionnez **GitHub Enterprise Managed User** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>Configurer et tester l’authentification unique Azure AD pour GitHub Enterprise Managed User

Pour configurer et tester l’authentification unique Azure AD avec GitHub Enterprise Managed User, effectuez les étapes suivantes :

1. **[Configurer l'authentification unique Azure AD](#configure-azure-ad-sso)**  : permet d'activer l'authentification unique SAML dans votre locataire AAD.
1. **[Configurer l'authentification unique GitHub Enterprise Managed User](#configure-github-enterprise-managed-user-sso)**  : permet de configurer les paramètres d'authentification unique dans GitHub Enterprise.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, dans la page d’intégration de l’application **GitHub Enterprise Managed User**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Avant de commencer, vérifiez que vous disposez de l'URL de votre instance d'Enterprise. Le champ ENTITY mentionné ci-dessous correspond au nom Enterprise de votre URL Enterprise compatible EMU. Par exemple, https://github.com/enterprises/contoso - **contoso** correspond au champ ENTITY. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://github.com/enterprises/<ENTITY>`
    
    > [!NOTE]
    > Notez que le format de l'identificateur est différent du format suggéré par l'application. Veuillez suivre le format ci-dessus. De plus, vérifiez que l'**identificateur ne contient pas de barre oblique finale.
    
    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://github.com/enterprises/<ENTITY>/saml/consume`
    

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://github.com/enterprises/<ENTITY>/sso`

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificate-base64-download.png)

1. Dans la section **Configurer GitHub Enterprise Managed User**, copiez les URL suivantes et enregistrez-les pour configurer GitHub ci-dessous.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez attribuer votre compte à GitHub Enterprise Managed User afin de finaliser la configuration de l'authentification unique.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **GitHub Enterprise Managed User**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez votre compte dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l'écran.
1. Dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle **Propriétaire Enterprise**, puis cliquez sur le bouton **Sélectionner** en bas de l'écran. Lorsque vous approvisionnez votre compte dans le tutoriel suivant, le rôle Propriétaire Enterprise lui est attribué pour votre instance de GitHub. 
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-github-enterprise-managed-user-sso"></a>Configurer l’authentification unique GitHub Enterprise Managed User

Pour configurer l'authentification unique côté **GitHub Enterprise Managed User**, vous devez disposer des éléments suivants :

1. URL de votre application AAD Enterprise Managed User ci-dessus : URL de connexion, identificateur Azure AD et URL de déconnexion.
1. Nom et mot de passe du compte du premier utilisateur administrateur de votre instance de GitHub Enterprise. Les informations d'identification sont fournies dans un e-mail de réinitialisation de mot de passe envoyé par votre contact GitHub Solutions Engineering. 

### <a name="enable-github-enterprise-managed-user-saml-sso"></a>Activer l'authentification unique SAML GitHub Enterprise Managed User

Dans cette section, vous allez utiliser les informations fournies par AAD ci-dessus et les entrer dans vos paramètres Enterprise pour activer la prise en charge de l'authentification unique.

1. Accédez à https://github.com
1. Cliquez sur Se connecter en haut à droite.
1. Entrez les informations d'identification du premier compte d'utilisateur administrateur. Le descripteur de connexion doit être au format suivant : `<your enterprise short code>_admin`
1. Accédez à https://github.com/enterprises/ `<your enterprise name>`. Ces informations doivent vous être fournies par votre contact Solutions Engineering.
1. Dans le menu de navigation de gauche, sélectionnez **Paramètres**, puis **Sécurité**.
1. Cochez la case **Activer l'authentification SAML**.
1. Entrez l'URL de connexion. Il s'agit de l'URL de connexion que vous avez précédemment copiée à partir d'AAD.
1. Entrez l'émetteur. Il s'agit de l'identificateur Azure AD que vous avez précédemment copié à partir d'AAD.
1. Entrez le certificat public. Ouvrez le certificat base64 que vous avez précédemment téléchargé et collez le texte du contenu de ce fichier dans cette boîte de dialogue.
1. Cliquez sur **Tester la configuration SAML**. Vous accédez alors à une boîte de dialogue qui vous permet de vous connecter à l'aide de vos informations d'identification Azure AD pour vérifier que l’authentification unique SAML est correctement configurée. Connectez-vous à l'aide de vos informations d'identification AAD. Si la validation est couronnée de succès, vous recevrez un message de type **Authentification réussie de votre identité d'authentification unique SAML**.
1. Cliquez sur **Enregistrer** pour enregistrer ces paramètres.
1. Enregistrez (téléchargez, imprimez ou copiez) les codes de récupération en lieu sûr.
1. Cliquez sur **Activer l'authentification SAML**.
1. À ce stade, seuls les comptes sur lesquels l'authentification unique est activée peuvent se connecter à votre instance d'Enterprise. Suivez les instructions du document ci-dessous pour approvisionner les comptes qui bénéficient de l'authentification unique.

## <a name="next-steps"></a>Étapes suivantes

GitHub Enterprise Managed User **exige** que tous les comptes soient créés par le biais de l'approvisionnement automatique des utilisateurs. Pour plus d'informations sur la configuration de l'approvisionnement automatique des utilisateurs, cliquez [ici](./github-enterprise-managed-user-provisioning-tutorial.md).
