---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Trend Micro Web Security(TMWS) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Trend Micro Web Security(TMWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083357"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Trend Micro Web Security(TMWS)

Dans ce tutoriel, vous allez découvrir comment intégrer Trend Micro Web Security(TMWS) à Azure Active Directory (Azure AD). Quand vous intégrez Trend Micro Web Security (TMWS) à Azure AD, vous pouvez :

* Contrôler qui dans Azure AD a accès à Trend Micro Web Security(TMWS).
* Permettre à vos utilisateurs de se connecter automatiquement à Trend Micro Web Security(TMWS) avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Trend Micro Web Security(TMWS) pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Trend Micro Web Security (TMWS) prend en charge l’authentification unique initiée par le **fournisseur de services**
* Après avoir configuré Trend Micro Web Security(TMWS), vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Ajout de Trend Micro Web Security (TMWS) à partir de la galerie

Pour configurer l’intégration de Trend Micro Web Security(TMWS) à Azure AD, vous devez ajouter Trend Micro Web Security(TMWS) à votre liste d’applications SaaS gérées à partir de la galerie.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Trend Micro Web Security(TMWS)** dans la zone de recherche.
1. Sélectionnez **Trend Micro Web Security(TMWS)** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Configurer et tester l’authentification unique Azure AD pour Trend Micro Web Security(TMWS)

Configurez et testez l’authentification unique Azure AD avec Trend Micro Web Security(TMWS) pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans Trend Micro Web Security(TMWS).

Pour configurer et tester l’authentification unique Azure AD avec Trend Micro Web Security(TMWS), suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
    1. **[Configurer les paramètres de synchronisation des utilisateurs et des groupes dans Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** – Configurer les paramètres de synchronisation des utilisateurs et des groupes dans Azure AD
1. **[Configurer l’authentification unique Trend Micro Web Security(TMWS)](#configure-trend-micro-web-security-sso)** pour configurer les paramètres de l’authentification unique côté application.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Trend Micro Web Security(TMWS)** , recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL : `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > La valeur de l'identificateur n'est pas réelle. Mettez à jour cette valeur avec l’identificateur réel. Vous pouvez récupérer ces valeurs dans la zone **Paramètres du fournisseur de services** du portail d’administration Azure, sur l’écran **Méthode d’authentification** pour Azure AD en accédant à **Administration > Services d’annuaire**.

1. L’application Trend Micro Web Security(TMWS) attend les mêmes assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Trend Micro Web Security(TMWS) s’attend à ce que quelques attributs supplémentaires (présentés ci-après) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom | Attribut source|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Trend Micro Web Security(TMWS)** , copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez permettre à B.Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Trend Micro Web Security(TMWS).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Trend Micro Web Security(TMWS)** .
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Configurer les paramètres de synchronisation des utilisateurs et des groupes dans Azure AD

1. À partir du volet de navigation gauche, cliquez sur **Azure Active Directory**.

1. Sous **Gérer**, cliquez sur **Inscriptions des applications**, puis sélectionnez la nouvelle application d’entreprise sous la zone **Toutes les applications**.

1. Sous **Gérer**, cliquez sur **Certificats et secrets**.

1. Sous la zone Secrets client qui apparaît, cliquez sur **Nouveau secret client**.

1. Dans l’écran Ajouter un secret client qui s’affiche, ajoutez éventuellement une description, sélectionnez une période d’expiration pour ce secret client, puis cliquez sur **Ajouter**. Le secret client nouvellement ajouté apparaît sous la zone Secrets client.

1. Enregistrez la valeur. Par la suite, vous taperez ces informations dans TMWS.

1. Sous **Gérer**, cliquez sur **Autorisations de l’API**. 

1. Dans l’écran Autorisations de l’API, cliquez sur le bouton **Ajouter une autorisation**.

1. Sous l’onglet API Microsoft de l’écran Demander des autorisations d’API qui s’affiche, cliquez sur **Microsoft Graph**, puis sur **Autorisations pour les applications**.

1. Recherchez et ajoutez les autorisations suivantes : 

    * Group.Read.All
    * User.Read.All

1. Cliquez sur **Ajouter des autorisations**. Un message s’affiche pour confirmer que vos paramètres ont bien été enregistrés. Les autorisations nouvellement ajoutées apparaissent dans l’écran Autorisations de l’API.

1. Sous la zone Donner son consentement, cliquez sur **Accorder un consentement d’administrateur pour < votre compte d’administrateur > (Répertoire par défaut)** , puis sur **Oui**. Un message s’affiche pour confirmer que le consentement d’administrateur a bien été accordé pour les autorisations demandées.

1. Cliquez sur **Overview**. 

1. Dans le panneau qui s’affiche à droite, enregistrez l’ID d’application (client) et l’ID de l’annuaire (locataire). Par la suite, vous taperez ces informations dans TMWS. Vous pouvez aussi cliquer sur **Noms de domaine personnalisés** sous Azure **Active Directory > Gérer** et enregistrer le nom de domaine dans le volet de droite.

## <a name="configure-trend-micro-web-security-sso"></a>Configurer l’authentification unique Trend Micro Web Security (TMWS)

1. Connectez-vous à la console de gestion TMWS et accédez à **Administration** > **USERS & AUTHENTICATION** > **Directory Services**.

1. Cliquez dans la partie supérieure de l’écran.

1. Dans l’écran Authentication Method qui s’affiche, cliquez sur **Azure AD**.

1. Cliquez sur **On** ou **Off** pour spécifier si les utilisateurs AD de votre organisation sont autorisés à accéder aux sites web par le biais de TMWS si leurs données ne sont pas synchronisées avec TMWS.

    > [!NOTE]
    > Les utilisateurs qui ne sont pas synchronisés à partir d’Azure AD peuvent être authentifiés uniquement par le biais de passerelles TMWS connues ou du port dédié à votre organisation.

1. Dans la section **Identity Provider Settings**, effectuez les étapes suivantes :

    a. Dans le champ **Service URL**, collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Dans le champ **Logon name attribute**, collez le nom de la revendication utilisateur avec l’attribut source **user.onpremisessamaccountname**, copié à partir du portail Azure.

    c. Dans le champ **Public SSL certificate**, utilisez le **certificat (en base64)** téléchargé (spécifié sur le portail Azure).

1. Dans la section **Synchronization Settings**, effectuez les étapes suivantes :

    a. Dans le champ **Tenant**, utilisez l’**ID d’annuaire (locataire)** ou le **nom de domaine personnalisé**, copié à partir du portail Azure.

    b. Dans le champ **Application ID**, spécifiez l’**ID d’application (client)** , copié à partir du portail Azure.

    c. Dans le champ **Client secret**, utilisez le **secret client** copié à partir du portail Azure.

    d. Dans le champ **Synchronization schedule**, sélectionnez le type de synchronisation avec Azure AD : manuelle ou selon une planification. Si vous choisissez la synchronisation manuelle, chaque fois que des modifications sont apportées aux informations des utilisateurs Active Directory, veillez à revenir à l’écran Directory Services et à effectuer une synchronisation manuelle pour que les informations de TMWS restent à jour.

    e. Cliquez sur **Test Connection** pour vérifier si le service Azure AD peut être connecté correctement. 
    
    f. Cliquez sur **Enregistrer**.
 
 > [!NOTE]
 > Pour plus d’informations sur la configuration de Trend Micro Web Security avec Azure AD, consultez [ce document](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Une fois que vous avez configuré le service Azure AD et spécifié Azure AD comme méthode d’authentification, vous pouvez vous connecter au serveur proxy TMWS pour vérifier votre configuration. Dès lors que votre compte a été vérifié par l’ouverture de session Azure AD, vous pouvez naviguer sur Internet.

> [!NOTE]
> TMWS ne prend pas en charge le test de l’authentification unique à partir du portail Azure AD, sous Vue d’ensemble > Authentification unique > Configurer l’authentification unique avec SAML > Tester votre nouvelle application d’entreprise.

1. Supprimez tous les cookies du navigateur, puis redémarrez ce dernier. 

1. Faites pointer votre navigateur vers le serveur proxy TMWS. Pour plus d’informations, consultez [Traffic Forwarding Using PAC Files](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Accédez à un site web sur Internet. TMWS vous dirige vers le portail captif TMWS.

1. Spécifiez un compte Active Directory (format : domain\sAMAccountName ou sAMAccountName@domain), une adresse e-mail ou un nom d’utilisateur principal (UPN), puis cliquez sur **Log On** (Ouvrir une session). TMWS vous dirige vers l’écran d’ouverture de session Azure AD.

1. Dans l’écran d’ouverture de session Azure AD, tapez les informations d’identification de votre compte AD. Vous devez être maintenant connecté à TMWS.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Trend Micro Web Security (TMWS) avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Guide pratique pour protéger Trend Micro Web Security(TMWS) avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

