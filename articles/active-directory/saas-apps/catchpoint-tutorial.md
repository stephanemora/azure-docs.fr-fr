---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Catchpoint | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968482"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Catchpoint

Dans ce tutoriel, vous allez découvrir comment intégrer Catchpoint à Azure AD (Azure Active Directory). Quand vous intégrez Catchpoint à Azure AD, vous pouvez :

* Contrôler qui a accès à Catchpoint dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Catchpoint avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Catchpoint pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Catchpoint prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.
* Catchpoint prend en charge le provisionnement **juste-à-temps** des utilisateurs.
* Une fois que vous avez configuré Catchpoint, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-catchpoint-from-the-gallery"></a>Ajout de Catchpoint à partir de la galerie

Pour configurer l’intégration de Catchpoint à Azure AD, vous devez ajouter Catchpoint, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Catchpoint** dans la zone de recherche.
1. Sélectionnez **Catchpoint** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Configurer et tester l’authentification unique Azure AD pour Catchpoint

Configurez et testez l’authentification unique Azure AD avec Catchpoint à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Catchpoint associé.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec Catchpoint, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Catchpoint](#configure-catchpoint-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Catchpoint](#create-catchpoint-test-user)** pour avoir un équivalent de B.Simon dans Catchpoint lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Catchpoint**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, entrez les valeurs pour le champ suivant :

    a. Dans la zone de texte **Identificateur**, tapez une l’URL : `https://portal.catchpoint.com/SAML2`

    b. Dans la zone **URL de réponse**, tapez l’URL : `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Votre application Catchpoint s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Catchpoint s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | ------------ | --------- |
    | espace de noms | user.assignedrole |

    > [!NOTE]
    > La revendication d’espace de noms doit être mappée avec le nom de compte. Ce nom de compte doit être configuré en tant que rôles dans Azure AD qui seront renvoyés dans la réponse SAML. Pour savoir comment configurer les rôles, reportez-vous à cet [article](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** et sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Catchpoint**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Catchpoint.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Catchpoint**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-catchpoint-sso"></a>Configurer l’authentification unique Catchpoint

1. Dans une autre fenêtre du navigateur web, connectez-vous à l’application Catchpoint en tant qu’administrateur.

1. Cliquez sur l’icône **Settings** (Paramètres), puis sélectionnez **SSO Identity Provider** (Fournisseur d’identité SSO).

    ![Configuration de Catchpoint](./media/catchpoint-tutorial/configuration1.png)

1. Dans la page **Single Sign On** (Authentification unique), effectuez les étapes suivantes :

    ![Configuration de Catchpoint](./media/catchpoint-tutorial/configuration2.png)

    1. Dans la zone de texte **Namespace** (Espace de noms), entrez une valeur d’espace de noms valide.

    1. Dans la zone de texte **Émetteur du fournisseur d’identité**, entrez l’**identificateur Azure AD** que vous avez copié à partir du portail Azure.

    1. Dans la zone de texte **Single Sign On Url** (URL d’authentification unique), entrez l’**URL de connexion**, que vous avez copiée sur le portail Azure.

    1. Dans le Bloc-notes, ouvrez le fichier **Certificat (Base64)** téléchargé, copiez le contenu du fichier de certificat, puis collez-le dans la zone de texte **Certificate**.

    1. Vous pouvez également charger le fichier **XML de métadonnées de fédération** en cliquant sur l’option **Upload Metadata** (Charger les métadonnées).

    1. Cliquez sur **Enregistrer**.

### <a name="create-catchpoint-test-user"></a>Créer un utilisateur de test Catchpoint

Dans cette section, un utilisateur appelé Britta Simon est créé dans Catchpoint. Catchpoint prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Catchpoint, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Catchpoint dans le volet d’accès, vous devez être automatiquement connecté à l’application Catchpoint pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Quand vous vous connectez à l’application Catchpoint par le biais de la page de connexion, après avoir fourni les **informations d’identification Catchpoint**, entrez la valeur d’espace de noms (**Namespace**) valide dans la zone de texte **Company Credentials(SSO)** (informations d’identification de la société (SSO)), puis cliquez sur **Login** (Se connecter).

![Configuration de Catchpoint](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Catchpoint avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)