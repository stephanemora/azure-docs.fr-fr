---
title: Configurer l’authentification unique – Azure Active Directory | Microsoft Docs
description: Ce didacticiel utilise le portail Azure pour configurer une authentification unique SAML pour une application avec Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: a415ac749d0d322bc2f71f64d4bec6e32ad1f12e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063490"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Comment configurer l’authentification unique SAML

Une fois que vous avez ajouté une application à vos applications d’entreprise Azure AD, vous configurez les paramètres d’authentification unique. Cet article explique comment configurer l’authentification unique SAML pour une application hors galerie. 

> [!NOTE]
> Vous ajoutez application de la galerie ? Découvrez des instructions de configuration pas à pas dans la [liste des tutoriels sur les applications SaaS](../saas-apps/tutorial-list.md)

Si vous voulez configurer l’authentification unique pour une application non issue de la galerie *sans écrire de code*, vous devez disposer d’un abonnement ou d’Azure AD Premium. De plus, l’application doit prendre en charge SAML 2.0. Pour plus d’informations sur les versions d’Azure AD, consultez [Tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Avant de commencer

- Si l’application n’a pas été ajoutée à votre locataire Azure AD, consultez [Ajouter une application figurant dans la galerie](add-gallery-app.md) ou [Ajouter une application ne figurant pas dans la galerie](add-non-gallery-app.md).
- Contactez le fournisseur de votre application pour obtenir les informations appropriées pour les paramètres suivants :

    | Paramètre de la configuration SAML de base | Initiée par SP | Initiée par idP | Description |
    |:--|:--|:--|:--|
    | Identificateur (ID d'entité) | Requis pour certaines applications | Requis pour certaines applications | Identifie de façon unique l’application pour laquelle l’authentification unique est en cours de configuration. Azure AD envoie l’identificateur à l’application en tant que paramètre Audience du jeton SAML. L’application est censée le valider. Cette valeur apparaît également en tant qu’ID d’entité dans les métadonnées SAML fournies par l’application. *Cette valeur correspond à l’élément **Émetteur** dans la demande SAML **AuthnRequest** envoyée par l’application.* |
    | URL de réponse | Facultatif | Obligatoire | Spécifie l’adresse où l’application attend le jeton SAML. L’URL de réponse est aussi appelée URL ACS (Assertion Consumer Service). |
    | URL de connexion | Obligatoire | Ne spécifiez pas | Lorsqu’un utilisateur ouvre cette URL, le fournisseur de services redirige vers Azure AD pour authentifier et connecter l’utilisateur. Azure AD utilise l’URL pour démarrer l’application à partir d’Office 365 ou du panneau d’accès Azure AD. Quand elle est vide, Azure AD s’appuie sur le fournisseur d’identité pour lancer l’authentification unique quand un utilisateur démarre l’application.|
    | État de relais | Facultatif | Facultatif | Indique à l’application où rediriger l’utilisateur une fois l’authentification terminée. En règle générale, la valeur est une URL valide pour l’application. Toutefois, certaines applications utilisent ce champ différemment. Pour plus d’informations, consultez le fournisseur de l’application.
    | URL de déconnexion | Facultatif | Facultatif | Utilisé pour renvoyer les réponses de déconnexion SAML à l’application.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Étape 1. Modifier la configuration SAML de base

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur d’application cloud ou administrateur d’application pour votre locataire Azure AD.

1. Accédez à **Azure Active Directory** > **Applications d’entreprise**, puis sélectionnez l’application dans la liste. 
   
   - Pour rechercher l’application, dans le menu **Type d’application**, sélectionnez **Toutes les applications**, puis sélectionnez **Appliquer**. Entrez le nom de l’application dans la zone de recherche, puis sélectionnez l’application dans les résultats.

1. Sous la section **Gérer**, sélectionnez **Authentification unique**. 

1. Sélectionnez **SAML**. La page **Configurer l’authentification unique avec SAML - Préversion** s’affiche.

1. Pour modifier les options de configuration SAML de base, sélectionnez l’icône **Modifier** (crayon) dans le coin supérieur droit de la section **Configuration SAML de base**.

     ![Configurer les certificats](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Dans les champs appropriés, entrez les informations décrites dans la section [Avant de commencer](#before-you-begin).

1. En haut de la page, sélectionnez **Enregistrer**.

## <a name="step-2-configure-user-attributes-and-claims"></a>Étape 2. Configurer les attributs et revendications d’un utilisateur 

Une application peut nécessiter des attributs ou des revendications utilisateur spécifiques dans le jeton SAML qu’elle reçoit d’Azure AD quand un utilisateur se connecte. Par exemple, des URI de revendication ou des valeurs de revendication spécifiques peuvent être nécessaires, ou **Name** peut devoir être différent du nom d’utilisateur stocké dans la plateforme d’identité Microsoft. Les exigences pour les applications de la galerie sont décrites dans les [tutoriels spécifiques aux applications](../saas-apps/tutorial-list.md) ; vous pouvez aussi demander ces informations au fournisseur de l’application. Les étapes générales de configuration des attributs et des revendications utilisateur sont décrites ci-dessous.

1. Dans la section **Attributs et revendications utilisateur**, sélectionnez l’icône **Modifier** (un crayon) dans le coin supérieur droit.

1. Vérifiez la **Valeur de nom d’identificateur**. La valeur par défaut est *utilisateur.nom_principal*. L’identificateur d’utilisateur identifie de façon unique chaque utilisateur au sein de l’application. Par exemple, si l’adresse e-mail est le nom d’utilisateur et l’identificateur unique, définissez la valeur sur *user.mail*.

1. Pour modifier la **Valeur d’identificateur de nom**, sélectionnez l’icône **Modifier** (crayon) pour le champ **Valeur d’identificateur de nom**. Apportez les modifications appropriées au format de l’identificateur et à la source. Pour plus d’informations, consultez [Modification de NameID](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Quand vous avez terminé, enregistrez les modifications. 
 
1. Pour configurer des revendications de groupe, sélectionnez l’icône **Modifier** pour le champ **Groupes retournés dans la revendication**. Pour plus d’informations, consultez [Configurer des revendications de groupe](../hybrid/how-to-connect-fed-group-claims.md).

3. Pour ajouter une revendication, sélectionnez **Ajouter une nouvelle revendication** en haut de la page. Entrez le **Nom** et sélectionnez la source appropriée. Si vous sélectionnez la source **Attribut**, vous devez choisir **l’attribut Source** à utiliser. Si vous sélectionnez la source **Translation**, vous devez choisir les éléments **Transformation** et **Parameter 1** à utiliser. Pour plus d’informations, consultez [Ajout de revendications spécifiques à l’application](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Quand vous avez terminé, enregistrez les modifications. 

4. Sélectionnez **Enregistrer**. La nouvelle revendication s’affiche dans la table.

   > [!NOTE]
   > Pour découvrir d’autres façons de personnaliser le jeton SAML d’Azure AD pour votre application, consultez les ressources suivantes.
   >- Pour créer des rôles personnalisés via le portail Azure, consultez [Configurer des revendications de rôle](../develop/active-directory-enterprise-app-role-management.md).
   >- Pour personnaliser les revendications via PowerShell, consultez [Personnaliser des revendications - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Pour modifier le manifeste d’application de façon à configurer des revendications facultatives pour votre application, consultez [Configurer des revendications facultatives](../develop/active-directory-optional-claims.md).
   >- Pour définir des stratégies de durée de vie des jetons pour les jetons d’actualisation, les jetons d’accès, les jetons de session et les jetons d’ID, consultez [Configurer les durées de vie des jetons](../develop/active-directory-configurable-token-lifetimes.md). Pour restreindre les sessions d’authentification via l’accès conditionnel Azure AD, consultez [Fonctionnalités de gestion des sessions d’authentification](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Étape 3. Gérer le certificat de signature SAML

Azure AD utilise un certificat pour signer les jetons SAML qu’il envoie à l’application. Dans la page **Configurer l’authentification unique avec SAML**, vous pouvez afficher ou télécharger le certificat actif. Vous pouvez également mettre à jour, créer ou importer un certificat. Pour les applications de la galerie, des détails sur le format du certificat sont disponibles dans la documentation SAML de l’application (consultez les [tutoriels spécifiques aux applications](../saas-apps/tutorial-list.md)). 

1. Accédez à la section **Certificat de signature SAML**. Selon le type d’application, vous verrez des options pour télécharger le certificat au format base64, au format brut ou au format XML de métadonnées de fédération. Azure AD fournit également l’**URL des métadonnées de fédération**, où vous pouvez accéder aux métadonnées spécifiques à l’application au format `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

1. Pour gérer, créer ou importer un certificat, sélectionnez l’icône **Modifier** (un crayon) dans le coin supérieur droit de la section **Certificat de signature SAML**, puis effectuez une des actions suivantes :

   - Pour créer un certificat, sélectionnez **Nouveau certificat**, sélectionnez la **Date d’expiration**, puis sélectionnez **Enregistrer**. Pour activer le certificat, sélectionnez le menu contextuel ( **...** ), puis sélectionnez **Définir comme certificat actif**.
   - Pour charger un certificat avec une clé privée et des informations d’identification PFX, sélectionnez **Importer un certificat** et accédez au certificat. Entrez le **Mot de passe PFX**, puis sélectionnez **Ajouter**.  
   - Pour configurer les options avancées de signature de certificat, utilisez les options suivantes. Pour des descriptions de ces options consultez l’article [Options avancées de signature de certificat](certificate-signing-options.md).
      - Dans la liste déroulante **Option de signature**, sélectionnez **Signer la réponse SAML**, **Signer l’assertion SAML** ou **Signer la réponse et l’assertion SAML**.
      - Dans la liste déroulante **Algorithme de signature**, choisissez **SHA-1** ou **SHA-256**.
   - Pour avertir d’autres personnes quand le certificat actif est proche de sa date d’expiration, entrez les adresses e-mail dans les champs **Adresses de courrier de notification**.

1. Sélectionnez **Enregistrer** en haut de la section **Certificat de signature SAML**. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Étape 4. Configurer l’application pour utiliser Azure AD

La section **Configurer\<nom_application** liste les valeurs qui doivent être configurées dans l’application afin qu’elle utilise Azure AD comme fournisseur d’identité SAML. Les valeurs requises dépendent de l’application. Pour plus d’informations, consultez la documentation SAML de l’application.

1. Faites défiler jusqu'à la section **Configurer \<applicationName >** . 
2. Copiez la valeur de chaque ligne de cette section en fonction des besoins et suivez les instructions spécifiques à l’application pour ajouter la valeur à l’application. Pour les applications de la galerie, vous pouvez voir la documentation en sélectionnant **Afficher les instructions détaillées**. 
   - Les valeurs **URL de connexion** et **URL de déconnexion** correspondent toutes les deux au même point de terminaison, qui est le point de terminaison de gestion des demandes SAML pour votre instance d'Azure AD. 
   - L’**Identificateur Azure AD** est la valeur de l’**Émetteur** dans le jeton SAML émis pour l’application.
1. Lorsque vous avez collé toutes les valeurs dans les champs appropriés, sélectionnez **Enregistrer**.

## <a name="step-5-validate-single-sign-on"></a>Étape 5. Valider l’authentification unique

Vous êtes prêt à tester les paramètres pour voir si l’authentification unique fonctionne pour vous, qui êtes l’administrateur.  

1. Ouvrez les paramètres d’authentification unique pour votre application. 
2. Faites défiler vers la section **Valider l’authentification unique avec <applicationName>** . Pour ce tutoriel, cette section est appelée **Configurer GitHub-test**.
3. Sélectionnez **Test**. Les options de tests s’affichent.
4. Sélectionnez **Se connecter en tant qu’utilisateur actuel**. 

Si l’authentification réussit, vous êtes prêt à affecter des utilisateurs et des groupes à votre application SAML.
Si un message d’erreur apparaît, effectuez les étapes suivantes :

1. Copiez et collez les détails dans la case **À quoi ressemble l’erreur ?** .

    ![Utilisez la zone « À quoi ressemble l’erreur » afin d’obtenir de l’aide pour sa résolution](media/configure-single-sign-on-portal/error-guidance.png)

1. Sélectionnez **Obtenir l’aide de résolution**. L’aide de résolution et de la cause racine apparaît.  Dans cet exemple, l’utilisateur n’était pas affecté à l’application.
1. Lisez l’aide de résolution et, si possible, corrigez le problème.
1. Réexécutez le test jusqu'à ce qu’il se termine correctement.

## <a name="next-steps"></a>Étapes suivantes

- [Affecter des utilisateurs et des groupes à l’application](methods-for-assigning-users-and-groups.md)
- [Configurer le provisionnement automatique d’un compte utilisateur](../app-provisioning/configure-automatic-user-provisioning-portal.md)
