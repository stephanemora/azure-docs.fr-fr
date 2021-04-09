---
title: 'Tutoriel : Configurer getAbstract pour le provisionnement automatique des utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure Active Directory vers getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102616126"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Tutoriel : Configurer getAbstract pour le provisionnement automatique des utilisateurs

Ce tutoriel décrit les étapes à suivre dans getAbstract et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique des utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes sur [getAbstract](https://www.getabstract.com) à l’aide du service de provisionnement Azure AD. Pour plus d’informations sur le rôle et le fonctionnement de ce service et pour accéder à la FAQ, consultez [Automatisation du provisionnement et du déprovisionnement des utilisateurs dans les applications SaaS (software as a service) avec Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Fonctionnalités prises en charge

> [!div class="checklist"]
> * Créer des utilisateurs dans getAbstract.
> * Supprimer des utilisateurs dans getAbstract lorsqu’ils n’ont plus besoin d’accès.
> * Conserver les attributs utilisateur synchronisés entre Azure AD et getAbstract.
> * Provisionner des groupes et des appartenances aux groupes dans getAbstract.
> * Activer [l’authentification unique (SSO)](getabstract-tutorial.md) auprès de getAbstract (recommandé).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md).
* Un compte d’utilisateur dans Azure AD disposant d’une [autorisation](../roles/permissions-reference.md) pour configurer le provisionnement. Exemples : Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général.
* Un locataire getAbstract (licence professionnelle getAbstract).
* Authentification unique activée sur le locataire Azure AD et le locataire getAbstract.
* Activation de l’approbation et de SCIM (System for Cross-domain Identity Management) pour getAbstract. (Envoyez un e-mail à b2b.itsupport@getabstract.com.)

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et getAbstract](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer getAbstract pour prendre en charge le provisionnement avec Azure AD

1. Connectez-vous à getAbstract.
1. Sélectionnez l’icône des paramètres située dans le coin supérieur droit, puis l’option **My Central Admin** (Mon administration centrale).

    ![Capture d’écran montrant My Central Admin (Mon administration centrale) sur getAbstract.](media/getabstract-provisioning-tutorial/my-account.png)

1. Recherchez et sélectionnez l’option **SCIM Admin** (Admin SCIM).

    ![Capture d’écran montrant SCIM Admin (Admin SCIM) sur getAbstract.](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Sélectionnez **Go**.

    ![Capture d’écran montrant l’ID client SCIM getAbstract.](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Sélectionnez **Générer un nouveau jeton**.

    ![Capture d’écran montrant le jeton SCIM getAbstract 1.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Si vous avez la certitude que vos informations sont correctes, sélectionnez **Generate new token** (Générer un nouveau jeton). Sinon, sélectionnez **Annuler**.

    ![Capture d’écran montrant le jeton SCIM getAbstract 2.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Sélectionnez l’icône de copie dans le Presse-papiers, ou bien sélectionnez l’intégralité du jeton et copiez-le. Remarquez au passage que l’URL du locataire/de base est `https://www.getabstract.com/api/scim/v2`. Ces valeurs seront entrées dans les zones **Jeton secret** et **URL du locataire** de l’onglet **Provisionnement** de votre application getAbstract sur le Portail Azure.

    ![Capture d’écran montrant le jeton SCIM getAbstract 3.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter getAbstract à partir de la galerie d’applications Azure AD

Ajoutez getAbstract à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans getAbstract. Si vous avez déjà configuré l’authentification SSO sur getAbstract, vous pouvez utiliser la même application. Nous vous recommandons de créer une application distincte lors du test initial de l’intégration. Pour savoir comment ajouter une application à partir de la galerie, consultez [ce démarrage rapide](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement

Vous pouvez utiliser le service d’approvisionnement Azure AD pour définir l’étendue des utilisateurs qui seront approvisionnés en fonction de l’affectation à l’application ou en fonction des attributs de l’utilisateur ou du groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue du provisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue (cf. [Provisionnement d’applications avec des filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)).

* Quand vous attribuez des utilisateurs et des groupes à getAbstract, vous devez sélectionner un rôle autre que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle d’accès par défaut sont exclus de l’attribution et sont marqués comme non autorisés dans les journaux de provisionnement. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles.

* Commencez progressivement. Effectuez un test avec un petit ensemble d’utilisateurs et de groupes avant de procéder à un déploiement général. Lorsque l’étendue du provisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cette option en attribuant un ou deux utilisateurs ou groupes à l’application. Quand l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur les attributs](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Étape 5. Configurer le provisionnement automatique des utilisateurs dans getAbstract

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs ou des groupes dans TestApp en fonction des affectations d’utilisateurs ou de groupes dans Azure AD.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Configuration du provisionnement automatique des utilisateurs pour getAbstract dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Capture d’écran montrant le volet Applications d’entreprise.](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **getAbstract**.

    ![Capture d’écran montrant le lien getAbstract dans la liste des applications.](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran montrant l’onglet Provisionnement.](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran montrant le Mode d’approvisionnement défini sur Automatique.](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification d’administration**, entrez vos informations **URL du locataire** et **Jeton secret** issues de getAbstract. Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à getAbstract. Si la connexion échoue, assurez-vous que votre compte getAbstract dispose des autorisations d’administrateur et réessayez.

    ![Capture d’écran montrant les zones URL du locataire et Jeton secret.](common/provisioning-testconnection-tenanturltoken.png)

1. Dans la zone **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur de provisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Capture d’écran montrant la zone E-mail de notification.](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec getAbstract**.

1. Dans la section **Mappages des attributs**, vérifiez les attributs des utilisateurs qui sont synchronisés d’Azure AD vers getAbstract. Les attributs sélectionnés en tant que propriétés **Matching** (Correspondance) sont utilisés pour faire correspondre les comptes d’utilisateurs dans getAbstract pour les opérations de mise à jour. Si vous modifiez [l’attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API getAbstract prend en charge le filtrage des utilisateurs en fonction de cet attribut. Sélectionnez **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |preferredLanguage|String|

1. Sous la section **Mappings** (Mappages), sélectionnez **Synchronize Azure Active Directory Groups to getAbstract** (Synchroniser les groupes Azure Active Directory avec getAbstract).

1. Dans la section **Mappages des attributs**, vérifiez les attributs des groupes qui sont synchronisés d’Azure AD vers getAbstract. Les attributs sélectionnés en tant que propriétés **Matching** (Correspondance) sont utilisés pour faire correspondre les groupes dans getAbstract pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications.

    |Attribut|Type|Pris en charge pour le filtrage|
    |---|---|---|
    |displayName|String|&check;|
    |externalId|String|
    |membres|Informations de référence|

1. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement Azure AD pour getAbstract, définissez le paramètre **État du provisionnement** sur **Activé** dans la section **Paramètres**.

    ![Capture d’écran montrant le paramètre État de provisionnement activé.](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et les groupes à provisionner dans getAbstract en sélectionnant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

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
