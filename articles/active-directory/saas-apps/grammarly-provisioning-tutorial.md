---
title: 'Tutoriel : Configurer Grammarly pour l’approvisionnement automatique d’utilisateur avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers Grammarly.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: thwimmer
ms.openlocfilehash: b0b880f2625f5893fe2b82111ab6f57d6af1b111
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326991"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Tutoriel : Configurer Grammarly pour l’approvisionnement automatique d’utilisateur

Ce tutoriel décrit les étapes à suivre dans Grammarly et dans Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateur. Une fois la configuration effectuée, Azure AD approvisionne et déprovisionne automatiquement des utilisateurs et des groupes dans [Grammarly](https://www.grammarly.com/) à l’aide du service d’approvisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Grammarly
> * Supprimer des utilisateurs dans Grammarly quand ils n’ont plus besoin d’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Grammarly

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général). 
* Un compte professionnel Grammarly avec accès administrateur.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Grammarly](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Grammarly pour prendre en charge l’approvisionnement avec Azure AD

Contactez votre représentant Grammarly ou écrivez à <support@grammarly.com> pour demander votre jeton d’approvisionnement.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Grammarly à partir de la galerie d’applications Azure AD

Ajoutez Grammarly à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement pour Grammarly. Si vous avez déjà configuré Grammarly pour l’authentification unique, vous pouvez utiliser la même application. Nous vous recommandons de créer une application distincte lors du test initial de l’intégration. Pour savoir comment ajouter une application à partir de la galerie, consultez [ce démarrage rapide](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement

Vous pouvez utiliser le service d’approvisionnement Azure AD pour définir l’étendue des utilisateurs qui seront approvisionnés en fonction de l’affectation à l’application ou en fonction des attributs de l’utilisateur ou du groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue du provisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue (cf. [Provisionnement d’applications avec des filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)).

* Quand vous attribuez des utilisateurs et des groupes à Grammarly, vous devez sélectionner un rôle autre que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle d’accès par défaut sont exclus de l’attribution et sont marqués comme non autorisés dans les journaux de provisionnement. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles.

* Commencez progressivement. Effectuez un test avec un petit ensemble d’utilisateurs et de groupes avant de procéder à un déploiement général. Lorsque l’étendue du provisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cette option en attribuant un ou deux utilisateurs ou groupes à l’application. Quand l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur les attributs](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateur pour Grammarly

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs ou des groupes dans TestApp en fonction des affectations d’utilisateurs ou de groupes dans Azure AD.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Configurer l’approvisionnement automatique d’utilisateur pour Grammarly dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Capture d’écran montrant le volet Applications d’entreprise.](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Grammarly**.

    ![Capture d’écran montrant le lien Grammarly dans la liste des applications.](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran montrant l’onglet Provisionnement.](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran montrant le Mode d’approvisionnement défini sur Automatique.](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification de l’administrateur**, dans le champ **URL de locataire**, entrez `https://sso.grammarly.com/scim/v2`, et dans le champ **Jeton secret**, entrez le jeton fourni par Grammarly (voir l’étape 2 ci-dessus). Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Grammarly. Si la connexion échoue, vérifiez que votre compte Grammarly dispose des autorisations d’administrateur, puis réessayez.

    ![Capture d’écran montrant les zones URL du locataire et Jeton secret.](common/provisioning-testconnection-tenanturltoken.png)

1. Dans la zone **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur de provisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Capture d’écran montrant la zone E-mail de notification.](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Gammarly**.

1. Dans la section **Mappages des attributs**, vérifiez les attributs d’utilisateurs qui sont synchronisés d’Azure AD vers Grammarly. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Grammarly pour les opérations de mise à jour. Si vous modifiez l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Grammarly prend en charge le filtrage des utilisateurs en fonction de cet attribut. Sélectionnez **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|


1. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service d’approvisionnement Azure AD pour Grammarly, dans la section **Paramètres**, définissez **État de l’approvisionnement** sur **Activé**.

    ![Capture d’écran montrant le paramètre État de provisionnement activé.](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et les groupes à approvisionner dans Grammarly en sélectionnant les valeurs souhaitées dans la section **Paramètres** sous **Étendue**.

    ![Capture d’écran montrant l’étendue du provisionnement.](common/provisioning-scope.png)

1. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Capture d’écran montrant le bouton Enregistrer.](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle initial prend plus de temps que les suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement

Après avoir configuré l’approvisionnement, utilisez les ressources suivantes pour superviser votre déploiement :

* Utilisez les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer pour quels utilisateurs le provisionnement a réussi et pour quels utilisateurs il a échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état d’avancement du cycle d’approvisionnement et le moment où il se terminera.
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour plus d’informations sur les états de quarantaine, consultez [Provisionnement d’application en état de quarantaine](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)