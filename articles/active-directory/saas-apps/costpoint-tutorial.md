---
title: 'Didacticiel : Intégration de Azure Active Directory à Costpoint | Microsoft Docs'
description: Découvrez comment configurer l'authentification unique entre Azure Active Directory et Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879596"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Didacticiel : Intégrer Costpoint à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Costpoint avec Azure Active Directory (Azure AD). Quand vous intégrez Costpoint à Azure AD, vous pouvez :

* Contrôler qui a accès à Costpoint dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Costpoint avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Costpoint pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. Costpoint prend en charge l’authentification unique lancée par le **fournisseur de services et le fournisseur d’identité**.

## <a name="adding-costpoint-from-the-gallery"></a>Ajout de Costpoint à partir de la galerie

Pour configurer l’intégration de Costpoint avec Azure AD, vous devez ajouter Costpoint, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Costpoint** dans la zone de recherche.
1. Sélectionnez **Costpoint** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Costpoint à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Costpoint associé.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec Costpoint, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer Costpoint](#configure-costpoint)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Costpoint](#create-costpoint-test-user)** pour avoir un équivalent de B. Simon dans Costpoint lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Costpoint**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    > [!NOTE]
    > Vous obtiendrez le fichier de métadonnées de fournisseur de services dans la section **Générer des métadonnées Costpoint** du tutoriel.
 
    1. Cliquez sur **Charger un fichier de métadonnées**.
    
    1. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.
    
    1. Une fois le fichier de métadonnées correctement chargé, les valeurs **Identificateur** et **URL de réponse** sont renseignées automatiquement dans les zones de texte de la section Costpoint

        > [!Note]
        > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins. Vérifiez que **Identificateur (ID d’entité)** et **URL de réponse (URL Assertion Consumer Service)** sont correctement définis, et que **URL ACS** est une URL Costpoint valide se terminant par **/LoginServlet.cps**.

    1. Cliquez sur **Définir des URL supplémentaires**.

    1. Dans la zone de texte **Relay State** (État du relais), tapez une valeur en respectant les formats suivants :`system=[your system], (for example, **system=DELTEKCP**)`

1. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, effectuez les étapes suivantes :
    
    Dans la zone de texte **URL de connexion**, tapez une URL : `https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’état du relais exacts. Pour obtenir ces valeurs, contactez [l’équipe du support client Costpoint](https://www.deltek.com/about/contact-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur l’icône Copier pour copier l’**URL des métadonnées de fédération d’application** et enregistrez-la dans le Bloc-notes.

   ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Générer des métadonnées Costpoint

La configuration de l’authentification unique SAML Costpoint est expliquée dans le guide **DeltekCostpoint711Security. pdf**. Dans ce guide, consultez la section **SAML Single Sign-on Setup -> Configure SAML Single Sign-on between Costpoint and Azure AD** (Configuration de l’authentification unique SAML -> Configurer l’authentification unique SAML entre Costpoint et Azure AD). Suivez les instructions et générez le fichier **XML de métadonnées de fédération du fournisseur de services Costpoint**. Utilisez ce fichier dans la **Configuration SAML de base** du portail Azure.

![Utilitaire de configuration de Costpoint](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> Vous obtiendrez le **Guide DeltekCostpoint711Security.pdf** auprès de l’[équipe de support Costpoint](https://www.deltek.com/about/contact-us). Si vous ne disposez pas de ce fichier, contactez-le pour l’obtenir.

### <a name="configure-costpoint"></a>Configurer Costpoint

Revenez à l’**Utilitaire de configuration de Costpoint** et collez l’**URL des métadonnées de fédération de l’application** dans la zone de texte **XML de métadonnées de fédération de fournisseur d’identité** et suivez les instructions du guide **DeltekCostpoint711Security.pdf** pour terminer la configuration SAML de Costpoint. 

![Utilitaire de configuration de Costpoint](./media/costpoint-tutorial/config01.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Costpoint.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Costpoint**.
1. Dans la section **Gérer** de la page Vue d’ensemble de l’ application, sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-costpoint-test-user"></a>Créer un utilisateur de test Costpoint

Dans cette section, vous créez un utilisateur dans Costpoint. Supposons que l’**ID d’utilisateur** est **B.SIMON** et que le nom est **B.Simon**. Collaborez avec l’[équipe de support technique Costpoint](https://www.deltek.com/about/contact-us) pour ajouter l’utilisateur dans la plateforme Costpoint. Les utilisateurs doivent être créés et activés pour que vous puissiez utiliser l’authentification unique.
 
Une fois créé, la sélection de la **Méthode d’authentification** de l’utilisateur doit être **Active Directory**, la case **Authentification unique SAML** doit être cochée et le nom d’utilisateur d’Azure Active Directory doit être **Active Directory ou un ID de certificat** (comme indiqué ci-dessous).

![Utilisateur Costpoint](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette Costpoint dans le panneau d’accès, vous devez être connecté automatiquement à l’application Costpoint pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)