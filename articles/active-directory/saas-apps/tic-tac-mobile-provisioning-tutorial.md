---
title: "Tutoriel : Configurer Tic-Tac Mobile pour l’approvisionnement automatique d'utilisateurs avec Azure Active Directory | Microsoft Docs"
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD sur Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357264"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Tutoriel : Configurer Tic-Tac Mobile pour l’approvisionnement automatique d'utilisateurs

Ce tutoriel décrit les étapes à suivre dans Tic-Tac Mobile et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD attribue et désattribue automatiquement les utilisateurs et les groupes dans [Tic-Tac Mobile](https://www.tictacmobile.com/) à l’aide du service de provisionnement Azure AD. Pour plus d’informations sur l’objet et le fonctionnement de ce service, et pour accéder aux questions fréquentes, consultez [Automatisation de l’attribution et de l’annulation d’attribution des utilisateurs pour les applications SaaS (Software as a Service) avec Azure AD](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge

> [!div class="checklist"]
> * Créer des utilisateurs dans Tic-Tac Mobile.
> * Supprimer des utilisateurs dans Tic-Tac Mobile quand ils n’ont plus besoin d’accès.
> * Maintenir les attributs utilisateur synchronisés entre Azure AD et Tic-Tac Mobile.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Un compte d’utilisateur dans Azure AD disposant d’une [autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) pour configurer le provisionnement. Par exemple : Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général.
* Un compte [Tic-Tac Mobile](https://www.tictacmobile.com/) avec un rôle de super administrateur.


## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Déterminez les données à [mapper entre Azure AD et Tic-Tac Mobile](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Tic-Tac Mobile pour prendre en charge l’approvisionnement avec Azure AD

Contactez support@tictacmobile.com pour obtenir votre **URL de locataire** et votre **Jeton secret**. Vous devez disposer d’un rôle de super administrateur dans Tic-Tac Mobile pour recevoir un jeton. Le jeton sera entré dans la zone **Jeton secret** sous l’onglet **Provisionnement** de votre application Tic-Tac Mobile dans le portail Azure.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Tic-Tac Mobile à partir de la galerie d’applications Azure AD

Ajoutez Tic-Tac Mobile à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement sur Tic-Tac Mobile. Si vous avez déjà configuré Tic-Tac Mobile pour l’authentification unique, vous pouvez utiliser la même application. Quand vous effectuez un test initial de l’intégration, créez une application distincte. Pour en savoir plus sur la façon d’ajouter une application à partir de la galerie, consultez [Provisionnement d’application basé sur des attributs avec des filtres d’étendue](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement

Avec le service de provisionnement Azure AD, vous pouvez définir l’étendue des utilisateurs qui seront attribués en fonction de l’affectation à l’application ou en fonction des attributs de l’utilisateur ou du groupe. Si vous choisissez de définir l’étendue des utilisateurs qui seront attribués à votre application en fonction de l’affectation, effectuez les étapes mentionnées dans [Gérer l’attribution d’utilisateurs pour une application dans Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) afin d’affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue des utilisateurs qui seront attribués uniquement en fonction des attributs de l’utilisateur ou du groupe, utilisez un filtre d’étendue comme décrit dans [Provisionnement d’application basé sur des attributs avec des filtres d’étendue](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Quand vous affectez des utilisateurs et des groupes à Tic-Tac Mobile, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle d’accès par défaut sont exclus de l’attribution et sont marqués comme non autorisés dans les journaux de provisionnement. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter plus de rôles.
* Commencez progressivement. Effectuez un test avec un petit ensemble d’utilisateurs et de groupes avant de procéder à un déploiement général. Quand l’étendue de l’attribution est définie sur les utilisateurs et les groupes affectés, vous pouvez garder le contrôle en affectant un ou deux utilisateurs ou groupes à l’application. Quand l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur les attributs](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Étape 5. Configurer l’approvisionnement automatique d'utilisateurs sur Tic-Tac Mobile

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs ou des groupes dans TestApp en fonction des affectations d’utilisateurs ou de groupes dans Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Configurer le provisionnement automatique d’utilisateurs pour Tic-Tac Mobile dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Capture d’écran montrant le volet Applications d’entreprise.](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Tic-Tac Mobile**.

    ![Capture d’écran montrant le lien Tic-Tac Mobile dans la liste Applications.](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran montrant l’onglet Provisionnement.](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran montrant l’option Automatique de l’onglet Provisionnement.](common/provisioning-automatic.png)

1. Sous la section **Informations d’identification de l’administrateur**, entrez l’**URL du locataire** et le **Jeton secret** de votre compte Tic-Tac Mobile. Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Tic-Tac Mobile. Si la connexion échoue, vérifiez que votre compte Tic-Tac Mobile dispose d’autorisations d’administrateur, puis réessayez.

    ![Capture d’écran montrant la zone Jeton secret.](common/provisioning-testconnection-tenanturltoken.png)

1. Dans la zone **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur de provisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Capture d’écran montrant la zone E-mail de notification.](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Tic-Tac Mobile**.

1. Dans la section **Mappage des attributs**, passez en revue les attributs d'utilisateurs synchronisés entre Azure AD et Tic-Tac Mobile. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d'utilisateur dans Tic-Tac Mobile pour les opérations de mise à jour. Si vous changez l’[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vous devez vérifier que l’API Tic-Tac Mobile prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |title|String|
   |Emails[type eq “work”].value|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Afin d’activer le service de provisionnement Azure AD pour Tic-Tac Mobile, définissez le paramètre **État de provisionnement** sur **Activé** dans la section **Paramètres**.

    ![Capture d’écran montrant le paramètre État de provisionnement activé.](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs ou groupes à provisionner dans Tic-Tac Mobile en sélectionnant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Capture d’écran montrant l’Étendue du provisionnement.](common/provisioning-scope.png)

1. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Capture d’écran montrant l’enregistrement de la configuration de provisionnement.](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement

Après avoir configuré le provisionnement, utilisez les ressources suivantes pour superviser votre déploiement.

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou ceux pour laquelle la procédure a échoué.
1. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état d’avancement du cycle d’approvisionnement et le moment où il se terminera.
1. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, consultez [Provisionnement d’application en état de quarantaine](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
