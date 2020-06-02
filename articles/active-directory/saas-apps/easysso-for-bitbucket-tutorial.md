---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à EasySSO pour BitBucket | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et EasySSO pour BitBucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ed96e97-b590-4dca-8d00-36288444c641
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5c89693c49e7a5f24308d82c7277ca0bc3a7ae2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83852994"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à EasySSO pour BitBucket

Dans ce tutoriel, vous allez apprendre à intégrer EasySSO pour BitBucket à Azure Active Directory (Azure AD). Quand vous intégrez EasySSO pour BitBucket à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à EasySSO pour BitBucket.
* Permettre aux utilisateurs de se connecter automatiquement à EasySSO pour BitBucket avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement EasySSO pour BitBucket pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* EasySSO pour BitBucket prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**
* EasySSO pour BitBucket prend en charge le provisionnement d’utilisateurs **juste-à-temps**
* Après avoir configuré EasySSO pour BitBucket, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-bitbucket-from-the-gallery"></a>Ajout d’EasySSO pour BitBucket à partir de la galerie

Pour configurer l’intégration d’EasySSO pour BitBucket dans Azure AD, vous devez ajouter EasySSO pour BitBucket, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **EasySSO pour BitBucket** dans la zone de recherche.
1. Sélectionnez **EasySSO pour BitBucket** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Configurer et tester l’authentification unique Azure AD pour EasySSO pour BitBucket

Configurez et testez l’authentification unique Azure AD avec EasySSO pour BitBucket pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur EasySSO pour BitBucket associé.

Pour configurer et tester l’authentification unique Azure AD avec EasySSO pour BitBucket, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique EasySSO pour BitBucket](#configure-easysso-for-bitbucket-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test EasySSO pour BitBucket](#create-easysso-for-bitbucket-test-user)** pour avoir dans EasySSO pour BitBucket un équivalent de B.Simon lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **EasySSO pour BitBucket**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique EasySSO pour BitBucket](mailto:support@techtime.co.nz). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Votre application EasySSO pour BitBucket attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application EasySSO pour BitBucket s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom |  Attribut source|
    | --------------- | --------- |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.42 | user.givenname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname|
    | urn:oid:0.9.2342.19200300.100.1.3 | user.userprincipalname |
    | | |

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EasySSO pour BitBucket.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **EasySSO pour BitBucket**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Configurer l’authentification unique côté EasySSO pour BitBucket

1. Connectez-vous à votre instance d’EasySSO pour BitBucket avec des privilèges d’administrateur et accédez à la section **Manage Apps** (Gérer les applications).

    ![Gérer les applications](./media/easysso-for-bitbucket-tutorial/jira-admin-1.png)

1. Cliquez sur **EasySSO**.

    ![Easy SSO](./media/easysso-for-bitbucket-tutorial/jira-admin-2.png)

1. Sélectionnez l’option **SAML**. Vous accédez alors à la section de configuration SAML.

    ![SAML](./media/easysso-for-bitbucket-tutorial/jira-admin-3.png)

1. Sélectionnez l’onglet **Certificates** en haut. L’écran suivant s’affiche. Recherchez le certificat (**Certificate (Base64)** ) ou le fichier de métadonnées (**Metadata File**) que vous avez enregistré lors des étapes précédentes de configuration de l’**authentification unique Azure AD**. Vous avez le choix entre les options suivantes :

    ![URL de métadonnées](./media/easysso-for-bitbucket-tutorial/jira-admin-4.png)

    a. Utilisez le **fichier de métadonnées** de fédération d’application que vous avez téléchargé dans un fichier local sur votre ordinateur. Sélectionnez la case d’option **Upload** (Charger) et suivez les instructions de la boîte de dialogue de chargement de fichier propre à votre système d’exploitation.

    **OU**

    b. Ouvrez le **fichier de métadonnées** de fédération d’application pour afficher le contenu (dans n’importe quel éditeur de texte brut) du fichier, et copiez-le dans le Presse-papiers. Sélectionnez l’option **Input** (Entrée) et collez le contenu du Presse-papiers dans le champ de texte.

    **OU**

    c. Configuration entièrement manuelle Ouvrez le **certificat (Base64)** de fédération d’application pour afficher le contenu (dans n’importe quel éditeur de texte brut) du fichier, et copiez-le dans le Presse-papiers. Collez-le dans le champ de texte **IdP Token Signing Certificates** (Certificats de signature de jetons IdP). Accédez ensuite à l’onglet **General** et renseignez les champs **POST Binding URL** (URL de liaison POST) et **Entity ID** (ID d’entité) avec les valeurs respectives d’**URL de connexion** et d’**Identificateur Azure AD** que vous avez enregistrées plus tôt.

1. Cliquez sur le bouton **Save** en bas de la page. Le contenu des fichiers de métadonnées ou de certificat sera alors analysé dans les champs de configuration. La configuration d’EasySSO for BitBucket est terminée.

1. Pour une expérience de test optimale, accédez à l’onglet **Look & Feel** (Apparence) et cochez l’option **SAML Login Button** (Bouton de connexion SAML). Un bouton distinct sera alors présent sur l’écran de connexion JIRA pour tester votre intégration SAML Azure AD de bout en bout. Vous pouvez également laisser ce bouton activé et configurer son emplacement, sa couleur et sa traduction pour le mode de production.

    ![Apparence](./media/easysso-for-bitbucket-tutorial/jira-admin-5.png)

    > [!NOTE]
    > En cas de problème, contactez l’[équipe de support EasySSO](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-bitbucket-test-user"></a>Créer un utilisateur de test EasySSO pour BitBucket

Dans cette section, un utilisateur appelé B.Simon est créé dans EasySSO for BitBucket. EasySSO for BitBucket prend en charge le provisionnement d’utilisateurs juste-à-temps, option **désactivée** par défaut. Pour activer le provisionnement d’utilisateurs, vous devez activer explicitement l’option **Create user on successful login** (Créer un utilisateur lors d’une connexion réussie) dans la section General de la configuration du plug-in EasySSO. S’il n’existe pas encore d’utilisateur dans EasySSO for BitBucket, il en est créé un après l’authentification.

Toutefois, si vous ne souhaitez pas activer le provisionnement automatique d’utilisateurs lors de la première connexion, les utilisateurs doivent exister dans les répertoires utilisateurs back-end utilisés par l’instance d’EasySSO pour BitBucket, comme LDAP ou Atlassian Crowd.

![Approvisionnement d'utilisateurs](./media/easysso-for-bitbucket-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette EasySSO pour BitBucket dans le volet d’accès, vous devez être automatiquement connecté à l’application EasySSO pour BitBucket pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer EasySSO pour BitBucket avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Guide pratique pour protéger EasySSO pour BitBucket avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
