---
title: 'Tutoriel : Configurer Insite LMS pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers Insite LMS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c4dbe83d-b5b4-4089-be89-b357e8d6f359
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2021
ms.author: Zhchia
ms.openlocfilehash: 31af7a6e3743272db629820f9130559f77bb6932
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103998"
---
# <a name="tutorial-configure-insite-lms-for-automatic-user-provisioning"></a>Tutoriel : Configurer Insite LMS pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Insite LMS et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes pour [Insite LMS](https://www.insite-it.net/) à l’aide du service de provisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Insite LMS
> * Supprimer les utilisateurs dans Insite LMS lorsqu’ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Insite LMS

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un [locataire Insite LMS](https://www.insite-it.net/).
* Un compte d’utilisateur Insite LMS avec des autorisations d’administrateur.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Insite LMS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-insite-lms-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Insite LMS pour prendre en charge le provisionnement avec Azure AD

1. Accédez à `https://portal.insitelms.net/<OrganizationName>`.
1. Téléchargez et installez le client de bureau.
1. Connectez-vous avec votre compte d’administrateur et accédez au module **Utilisateurs**.
1. Sélectionnez l’utilisateur `scim@insitelms.net` et appuyez sur le bouton **Générer un jeton d’accès**. Si vous ne trouvez pas l’utilisateur scim-User, contactez l’équipe de support
    1.  Choisissez **AzureAdScimProvisioning** et appuyez sur **Générer** 
    1.  Copiez le **jeton d’accès**
1. L’**URL du locataire** est `https://web.insitelms.net/<OrganizationName>/api/scim`.

## <a name="step-3-add-insite-lms-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Insite LMS à partir de la galerie d’applications Azure AD

Ajoutez Insite LMS à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement sur Insite LMS. Si vous avez déjà configuré Insite LMS pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous attribuez des utilisateurs et des groupes à Insite LMS, vous devez sélectionner un autre rôle qu’**Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-insite-lms"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs sur Insite LMS 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans l’application Insite LMS, selon les attributions d’utilisateurs et de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insite-lms-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Insite LMS dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Insite LMS**.

    ![Lien Insite LMS dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1.  Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification d’administration**, entrez vos informations **URL du locataire** et **Jeton secret** issues d’Insite LMS. Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Insite LMS. Si la connexion échoue, vérifiez que votre compteInsite LMS dispose d’autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Insite LMS**.

1. Dans la section **Mappages des attributs**, consultez les attributs utilisateur qui sont synchronisés entre Azure AD et Insite LMS. Les attributs sélectionnés en tant que propriétés **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Insite LMS pour les opérations de mise à jour. Si vous modifiez l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Insite LMS prend en charge le filtrage des utilisateurs en fonction de cet attribut. Sélectionnez **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |emails[type eq "work"].value|String|&check;|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|

1. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement d’Azure AD pour Insite LMS, dans la section **Paramètres**, définissez **État du provisionnement** sur **Activé**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et les groupes à provisionner dans Insite LMS en sélectionnant les valeurs souhaitées sous **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle initial prend plus de temps que les suivants, qui se produisent toutes les 40 minutes environ tant que le service d’approvisionnement d’Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement

Après avoir configuré l’approvisionnement, utilisez les ressources suivantes pour superviser votre déploiement :

* Utilisez les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer pour quels utilisateurs le provisionnement a réussi et pour quels utilisateurs il a échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et sa progression.
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour plus d’informations sur les états de quarantaine, consultez [Provisionnement d’application en état de quarantaine](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)