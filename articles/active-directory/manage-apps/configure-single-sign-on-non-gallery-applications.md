---
title: Authentification unique SAML - applications hors galerie - Plateforme d’identité Microsoft | Microsoft Docs
description: Configurer l’authentification unique (SSO) sur des applications hors galerie dans la plateforme d’identité Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159061"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Configurer l’authentification unique SAML pour des applications non issues de la galerie

Lorsque vous [ajoutez une application galerie](add-gallery-app.md) ou une [application web non galerie](add-non-gallery-app.md) à vos applications d'entreprise Azure AD, l'une des options de connexion unique disponibles est [authentification unique SAML](what-is-single-sign-on.md#saml-sso). Choisissez si possible SAML pour les applications qui s’authentifient par le biais d’un protocole SAML.gestion des actifs logiciels. Avec l’authentification unique SAML, Azure AD s’authentifie dans l’application en utilisant le compte Azure AD de l’utilisateur. Azure AD communique les informations d’authentification à l’application via un protocole de connexion. Vous pouvez mapper les utilisateurs à des rôles d’application basés sur les règles, que vous définissez dans vos revendications SAML. Cet article explique comment configurer l’authentification unique SAML pour une application hors galerie. 

> [!NOTE]
> Vous ajoutez application de la galerie ? Découvrez des instructions de configuration pas à pas dans la [liste des tutoriels sur les applications SaaS](../saas-apps/tutorial-list.md)

Si vous voulez configurer l’authentification unique SAML pour une application non issue de la galerie sans écrire de code, vous devez disposer d’un abonnement et d’une licence Azure AD Premium. De plus, l’application doit prendre en charge SAML 2.0. Pour plus d’informations sur les versions d’Azure AD, consultez [Tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Avant de commencer

Si l’application n’a pas été ajoutée à votre locataire Azure AD, consultez [Ajouter une application non-galerie](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Étape 1. Modifier la configuration SAML de base

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur d’application cloud ou administrateur d’application pour votre locataire Azure AD.

2. Accédez à **Azure Active Directory** > **Applications d’entreprise**, puis sélectionnez l’application dans la liste. 
   
   - Pour rechercher l’application, dans le menu **Type d’application**, sélectionnez **Toutes les applications**, puis sélectionnez **Appliquer**. Entrez le nom de l’application dans la zone de recherche, puis sélectionnez l’application dans les résultats.

3. Sous la section **Gérer**, sélectionnez **Authentification unique**. 

4. Sélectionnez **SAML**. La page **Configurer l’authentification unique avec SAML - Préversion** s’affiche.

   ![Étape 1 Modifier la configuration SAML de base](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Pour modifier les options de configuration SAML de base, sélectionnez l’icône **Modifier** (crayon) dans le coin supérieur droit de la section **Configuration SAML de base**.

1. Entrez les paramètres suivants. Les valeurs doivent vous être communiquées par le fournisseur de l’application. Vous pouvez entrer manuellement les valeurs ou charger un fichier de métadonnées pour extraire la valeur des champs.

    | Paramètre de la configuration SAML de base | Initiée par SP | Initiée par idP | Description |
    |:--|:--|:--|:--|
    | **Identificateur (ID d'entité)** | Requis pour certaines applications | Requis pour certaines applications | Identifie l’application de manière unique. Azure AD envoie l’identificateur à l’application en tant que paramètre Audience du jeton SAML. L’application est censée le valider. Cette valeur apparaît également en tant qu’ID d’entité dans les métadonnées SAML fournies par l’application. Entrez une URL dont le modèle est le suivant : « https://<subdomain>.contoso.com ». *Cette valeur correspond à l’élément **Émetteur** dans la demande SAML **AuthnRequest** envoyée par l’application.* |
    | **URL de réponse** | Obligatoire | Obligatoire | Spécifie l’adresse où l’application attend le jeton SAML. L’URL de réponse est aussi appelée URL ACS (Assertion Consumer Service). Vous pouvez utiliser les champs URL de réponse supplémentaires pour spécifier plusieurs URL de réponse. Par exemple, vous pourriez avoir besoin d'URL de réponse supplémentaires pour plusieurs sous-domaines. Ou, à des fins de test, vous pouvez spécifier simultanément plusieurs URL de réponse (hôte local et URL publiques). |
    | **URL d’authentification** | Obligatoire | Ne spécifiez pas | Lorsqu’un utilisateur ouvre cette URL, le fournisseur de services redirige vers Azure AD pour authentifier et connecter l’utilisateur. Azure AD utilise l’URL pour démarrer l’application à partir d’Office 365 ou du panneau d’accès Azure AD. Lorsqu’elle est vide, Azure AD effectue une authentification initiée par le fournisseur d'identité lorsqu’un utilisateur lance l’application à partir d’Office 365, du panneau d’accès Azure AD ou de l’URL d’authentification unique SSO Azure AD.|
    | **État de relais** | Facultatif | Facultatif | Indique à l’application où rediriger l’utilisateur une fois l’authentification terminée. En règle générale, la valeur est une URL valide pour l’application. Toutefois, certaines applications utilisent ce champ différemment. Pour plus d’informations, consultez le fournisseur de l’application.
    | **URL de déconnexion** | Facultatif | Facultatif | Utilisé pour renvoyer les réponses de déconnexion SAML à l’application.

Pour plus d’informations, consultez [Protocole SAML d’authentification unique](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Étape 2. Configurer les attributs et revendications d’un utilisateur 

Lorsqu’un utilisateur s’authentifie auprès de l’application, Azure AD émet pour l’application un jeton SAML contenant des informations (ou des revendications) sur l’utilisateur qui l’identifient de façon unique. Par défaut, ces informations incluent le nom d’utilisateur, son adresse e-mail, son prénom et son nom. Vous devrez peut-être personnaliser ces revendications si, par exemple, l'application nécessite des valeurs de revendications spécifiques ou un format **Nom** autre que le nom d'utilisateur. Les exigences pour les applications de la galerie sont décrites dans les [tutoriels spécifiques aux applications](../saas-apps/tutorial-list.md) ; vous pouvez aussi demander ces informations au fournisseur de l’application. Les étapes générales de configuration des attributs et des revendications utilisateur sont décrites ci-dessous.

1. Dans la section **Attributs et revendications utilisateur**, sélectionnez l’icône **Modifier** (un crayon) dans le coin supérieur droit.

   ![Étape 2 Configurer les attributs et revendications d’un utilisateur](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Vérifiez la **Valeur de nom d’identificateur**. La valeur par défaut est *utilisateur.nom_principal*. L’identificateur d’utilisateur identifie de façon unique chaque utilisateur au sein de l’application. Par exemple, si l’adresse e-mail est le nom d’utilisateur et l’identificateur unique, définissez la valeur sur *user.mail*.

3. Pour modifier la **Valeur d’identificateur de nom**, sélectionnez l’icône **Modifier** (crayon) pour le champ **Valeur d’identificateur de nom**. Apportez les modifications appropriées au format de l’identificateur et à la source. Pour plus d’informations, consultez [Modification de NameID](../develop/active-directory-saml-claims-customization.md#editing-nameid). Quand vous avez terminé, enregistrez les modifications. 
 
4. Pour configurer des revendications de groupe, sélectionnez l’icône **Modifier** pour le champ **Groupes retournés dans la revendication**. Pour plus d’informations, consultez [Configurer des revendications de groupe](../hybrid/how-to-connect-fed-group-claims.md).

5. Pour ajouter une revendication, sélectionnez **Ajouter une nouvelle revendication** en haut de la page. Entrez le **Nom** et sélectionnez la source appropriée. Si vous sélectionnez la source **Attribut**, vous devez choisir **l’attribut Source** à utiliser. Si vous sélectionnez la source **Translation**, vous devez choisir les éléments **Transformation** et **Parameter 1** à utiliser. Pour plus d’informations, consultez [Ajout de revendications spécifiques à l’application](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Quand vous avez terminé, enregistrez les modifications. 

6. Sélectionnez **Enregistrer**. La nouvelle revendication s’affiche dans la table.

   > [!NOTE]
   > Pour découvrir d’autres façons de personnaliser le jeton SAML d’Azure AD pour votre application, consultez les ressources suivantes.
   >- Pour créer des rôles personnalisés via le portail Azure, consultez [Configurer des revendications de rôle](../develop/active-directory-enterprise-app-role-management.md).
   >- Pour personnaliser les revendications via PowerShell, consultez [Personnaliser des revendications - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Pour modifier le manifeste d’application de façon à configurer des revendications facultatives pour votre application, consultez [Configurer des revendications facultatives](../develop/active-directory-optional-claims.md).
   >- Pour définir des stratégies de durée de vie des jetons pour les jetons d’actualisation, les jetons d’accès, les jetons de session et les jetons d’ID, consultez [Configurer les durées de vie des jetons](../develop/active-directory-configurable-token-lifetimes.md). Pour restreindre les sessions d’authentification via l’accès conditionnel Azure AD, consultez [Fonctionnalités de gestion des sessions d’authentification](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Étape 3. Gérer le certificat de signature SAML

Azure AD utilise un certificat pour signer les jetons SAML qu’il envoie à l’application. Vous aurez besoin de ce certificat pour configurer l’approbation entre Azure AD et l’application. Pour plus d’informations sur le format du certificat, consultez la documentation SAML de l’application. Pour plus d’informations, consultez [Gérer des certificats pour l’authentification unique fédérée](manage-certificates-for-federated-single-sign-on.md) et [Options avancées de signature de certificats dans le jeton SAML](certificate-signing-options.md).

Depuis Azure AD, vous pouvez télécharger le certificat actif au format Base64 ou Raw directement à partir de la page principale **Configurer l’authentification unique avec SAML**. Sinon, vous pouvez le récupérer en téléchargeant le fichier XML de métadonnées de l’application ou en utilisant l’URL des métadonnées de fédération de l’application. Pour afficher, créer ou télécharger vos certificats (actifs ou inactifs), procédez comme suit.

1. Accédez à la section **Certificat de signature SAML**. 

   ![Étape 3 Gérer le certificat de signature SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Vérifiez que le certificat comporte :

   - *La date d’expiration souhaitée.* Vous pouvez configurer une date d’expiration allant jusqu’à trois ans dans le futur.
   - *Un état actif pour le certificat souhaité.* Si l’état est **inactif**, définissez-le sur **Actif**. Pour modifier l’état, cliquez sur la ligne du certificat souhaité avec le bouton droit de la souris, puis sélectionnez **Définir comme certificat actif**.
   - *L’option et l’algorithme de signature appropriés.*
   - *Les adresses e-mail de notification correctes.* Lorsque le certificat actif est proche de sa date d’expiration, Azure AD envoie une notification à l’adresse e-mail configurée dans ce champ.

2. Pour télécharger le certificat, sélectionnez l'une des options suivantes pour le format Base64, le format Raw ou le fichier XML de métadonnées de fédération. Azure AD fournit également l’**URL des métadonnées de fédération**, où vous pouvez accéder aux métadonnées spécifiques à l’application au format `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Pour gérer, créer ou importer un certificat, sélectionnez l’icône **Modifier** (un crayon) dans le coin supérieur droit de la section **Certificat de signature SAML**.

   ![Certificat de signature SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Effectuez l'une des opérations suivantes :

   - Pour créer un certificat, sélectionnez **Nouveau certificat**, sélectionnez la **Date d’expiration**, puis sélectionnez **Enregistrer**. Pour activer le certificat, sélectionnez le menu contextuel ( **...** ), puis sélectionnez **Définir comme certificat actif**.
   - Pour charger un certificat avec une clé privée et des informations d’identification PFX, sélectionnez **Importer un certificat** et accédez au certificat. Entrez le **Mot de passe PFX**, puis sélectionnez **Ajouter**.  
   - Pour configurer les options avancées de signature de certificat, utilisez les options suivantes. Pour des descriptions de ces options consultez l’article [Options avancées de signature de certificat](certificate-signing-options.md).
      - Dans la liste déroulante **Option de signature**, sélectionnez **Signer la réponse SAML**, **Signer l’assertion SAML** ou **Signer la réponse et l’assertion SAML**.
      - Dans la liste déroulante **Algorithme de signature**, choisissez **SHA-1** ou **SHA-256**.
   - Pour avertir d’autres personnes quand le certificat actif est proche de sa date d’expiration, entrez les adresses e-mail dans les champs **Adresses de courrier de notification**.

4. Si vous avez effectué des modifications, sélectionnez **Enregistrer** en haut de la section **Certificat de signature SAML**. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Étape 4. Configurer l’application pour utiliser Azure AD

La section **Configurer\<nom_application** liste les valeurs qui doivent être configurées dans l’application afin qu’elle utilise Azure AD comme fournisseur d’identité SAML. Les valeurs requises dépendent de l’application. Pour plus d’informations, consultez la documentation SAML de l’application. Pour trouver la documentation, accédez à la rubrique **Configurer \<nom de l’application>** et sélectionnez **Afficher les instructions détaillées**. La documentation s’affiche dans la page **Configurer l’authentification**. Cette page vous aide à renseigner l’**URL de connexion**, l’**Identificateur Azure AD** et l’**URL de déconnexion** dans la rubrique **Configurer \<nom de l’application >** .

1. Faites défiler jusqu'à la section **Configurer \<applicationName >** . 
   
   ![Étape 4 Configurer l’application](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Copiez la valeur de chaque ligne de cette section en fonction des besoins et suivez les instructions spécifiques à l’application pour ajouter la valeur à l’application. Pour les applications de la galerie, vous pouvez voir la documentation en sélectionnant **Afficher les instructions détaillées**. 
   - Les valeurs **URL de connexion** et **URL de déconnexion** correspondent toutes les deux au même point de terminaison, qui est le point de terminaison de gestion des demandes SAML pour votre instance d'Azure AD. 
   - L’**Identificateur Azure AD** est la valeur de l’**Émetteur** dans le jeton SAML émis pour l’application.
2. Lorsque vous avez collé toutes les valeurs dans les champs appropriés, sélectionnez **Enregistrer**.

## <a name="step-5-validate-single-sign-on"></a>Étape 5. Valider l’authentification unique

Une fois que vous avez configuré votre application pour utiliser Azure AD en tant que fournisseur d'identité basé sur SAML, vous pouvez tester les paramètres pour vérifier le fonctionnement de la connexion unique avec votre compte. 

2. Faites défiler vers la section **Valider l’authentification unique avec <applicationName>** .

   ![Étape 5 Valider l’authentification unique](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Sélectionnez **Valider**. Les options de tests s’affichent.

4. Sélectionnez **Se connecter en tant qu’utilisateur actuel**. 

Si l’authentification réussit, vous êtes prêt à affecter des utilisateurs et des groupes à votre application SAML.
Si un message d’erreur apparaît, effectuez les étapes suivantes :

1. Copiez et collez les détails dans la case **À quoi ressemble l’erreur ?** .

    ![Obtenir l’aide de résolution](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Sélectionnez **Obtenir l’aide de résolution**. L’aide de résolution et de la cause racine apparaît.  Dans cet exemple, l’utilisateur n’était pas affecté à l’application.

3. Lisez l’aide de résolution et, si possible, corrigez le problème.

4. Réexécutez le test jusqu'à ce qu’il se termine correctement.

Pour plus d’informations, voir [Guide pratique pour déboguer l’authentification unique SAML pour les applications dans Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Étapes suivantes

- [Affecter des utilisateurs et des groupes à l’application](methods-for-assigning-users-and-groups.md)
- [Configurer le provisionnement automatique d’un compte utilisateur](../app-provisioning/configure-automatic-user-provisioning-portal.md)
