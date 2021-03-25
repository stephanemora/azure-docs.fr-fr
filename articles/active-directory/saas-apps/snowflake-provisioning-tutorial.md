---
title: 'Tutoriel : Configurer Snowflake pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes d’utilisateur sur Snowflake.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539533"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutoriel : Configurer Snowflake pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel présente les étapes à effectuer dans Snowflake et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et des groupes sur [Snowflake](https://www.Snowflake.com/pricing/). Pour découvrir les informations importantes sur ce que fait ce service, savoir comment il fonctionne et consulter les questions courantes, consultez [Qu’est-ce que le provisionnement automatique des utilisateurs d’applications SaaS dans Azure AD ?](../app-provisioning/user-provisioning.md) 

> [!NOTE]
> Ce connecteur est actuellement en préversion publique. Pour obtenir des informations sur les conditions d’utilisation, consultez [Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Snowflake
> * Supprimer des utilisateurs dans Snowflake quand ils ne nécessitent plus d’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Snowflake
> * Provisionner des groupes et des appartenances aux groupes dans Snowflake
> * Autoriser l’[authentification unique](./snowflake-tutorial.md) auprès de Snowflake (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md)
* Dans Azure AD, un compte d’utilisateur disposant de l’[autorisation](../roles/permissions-reference.md) de configurer le provisionnement (Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général)
* [Un locataire Snowflake](https://www.Snowflake.com/pricing/)
* Un compte d’utilisateur dans Snowflake avec des autorisations d’administrateur

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1 : Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Étape 2 : Configurer Snowflake pour prendre en charge le provisionnement avec Azure AD

Avant de configurer Snowflake pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez activer le provisionnement SCIM (System for Cross-domain Identity Management) dans Snowflake.

1. Connectez-vous à votre console d’administration Snowflake. Entrez la requête suivante dans la feuille de calcul mise en évidence, puis sélectionnez **Exécuter**.

    ![Capture d’écran de la console d’administration Snowflake montrant la requête et le bouton Exécuter.](media/Snowflake-provisioning-tutorial/image00.png)

2.  Un jeton d’accès SCIM est généré pour votre locataire Snowflake. Pour le récupérer, sélectionnez le lien mis en évidence dans la capture d’écran suivante.

    ![Capture d’écran d’une feuille de calcul dans l’interface utilisateur Snowflake avec le jeton d’accès SCIM mis en évidence.](media/Snowflake-provisioning-tutorial/image01.png)

3. Copiez la valeur du jeton généré, puis sélectionnez **Terminé**. Cette valeur est entrée dans la zone **Jeton secret** située sous l’onglet **Provisionnement** de votre application Snowflake dans le portail Azure.

    ![Capture d’écran de la section Détails montrant le jeton copié dans le champ de texte et l’option Terminé mise en évidence.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Étape 3 : Ajouter Snowflake à partir de la galerie d’applications Azure AD

Ajoutez Snowflake à partir de la galerie d’applications Azure AD pour gérer le provisionnement dans Snowflake. Si vous avez déjà configuré Snowflake pour l’authentification unique (SSO), vous pouvez utiliser la même application. Toutefois, nous vous recommandons de créer une application distincte quand vous testez initialement l’intégration. [En savoir plus sur l’ajout d’une application à partir de la galerie](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4 : Définir qui sera dans l’étendue pour l’approvisionnement 

Le service de provisionnement Azure AD vous permet de définir l’étendue des utilisateurs qui seront provisionnés en fonction de l’affectation à l’application, ou en fonction des attributs de l’utilisateur ou du groupe. Si vous choisissez de définir l’étendue des utilisateurs qui seront provisionnés pour votre application en fonction de l’attribution, vous pouvez utiliser les [étapes permettant d’affecter des utilisateurs et des groupes à l’application](../manage-apps/assign-user-or-group-access-portal.md). Si vous choisissez de définir l’étendue des utilisateurs qui seront provisionnés uniquement en fonction des attributs de l’utilisateur ou du groupe, vous pouvez [utiliser un filtre d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Gardez à l’esprit les conseils suivants :

* Quand vous attribuez des utilisateurs et des groupes à Snowflake, vous devez sélectionner un rôle autre que le rôle Accès par défaut. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle Accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’attribution est définie sur les utilisateurs et les groupes affectés, vous pouvez contrôler cet aspect en affectant un ou deux utilisateurs ou groupes à l’application. Quand l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur les attributs](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Étape 5 : Configurer l’approvisionnement automatique d’utilisateurs sur Snowflake 

Cette section vous guide tout au long des étapes à suivre pour configurer le service de provisionnement Azure AD afin de créer, de mettre à jour et de désactiver des utilisateurs et des groupes dans Snowflake. Vous pouvez baser la configuration sur les affectations d’utilisateurs et de groupes dans Azure AD.

Pour configurer l’approvisionnement automatique d’utilisateurs pour Snowflake dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Capture d’écran montrant le volet Applications d’entreprise.](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Snowflake**.

    ![Capture d’écran montrant une liste d’applications.](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de la liste déroulante Mode de provisionnement avec l’option Automatique en évidence.](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez les valeurs d’URL de base SCIM 2.0 et de jeton d’authentification que vous avez récupérées précédemment dans les zones **URL de locataire** et **Jeton secret**, respectivement. 

   Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Snowflake. Si la connexion échoue, vérifiez que votre compte Snowflake dispose d’autorisations d’administrateur, puis réessayez.

    ![Capture d’écran montrant les zones de l’URL du locataire et du jeton secret, avec le bouton Tester la connexion.](common/provisioning-testconnection-tenanturltoken.png)

6. Dans la zone **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur de provisionnement. Cochez ensuite la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Capture d’écran montrant la zone et la case à cocher pour l’e-mail de notification.](common/provisioning-notification-email.png)

7. Sélectionnez **Save** (Enregistrer).

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Snowflake**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs d’utilisateurs qui sont synchronisés entre Azure AD et Snowflake. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Snowflake pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|
   |---|---|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Snowflake**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Snowflake. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Snowflake pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    |Attribut|Type|
    |---|---|
    |displayName|String|
    |membres|Informations de référence|

12. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour Snowflake, définissez **État du provisionnement** sur **Activé** dans la section **Paramètres**.

     ![Capture d’écran montrant le paramètre État du provisionnement activé.](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et les groupes que vous voulez provisionner sur Snowflake en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**. 

    Si cette option n’est pas disponible, configurez les champs nécessaires sous **Informations d’identification de l’administrateur**, sélectionnez **Enregistrer**, puis actualisez la page. 

     ![Capture d’écran montrant les choix disponibles pour l’étendue du provisionnement.](common/provisioning-scope.png)

15. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

     ![Capture d’écran du bouton permettant d’enregistrer une configuration de provisionnement.](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Les synchronisations suivantes se produisent toutes les 40 minutes environ, tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6 : Surveiller votre déploiement
Après avoir configuré l’approvisionnement, utilisez les ressources suivantes pour superviser votre déploiement :

- Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou ceux pour laquelle la procédure a échoué.
- Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état d’avancement du cycle d’approvisionnement et le moment où il se terminera.
- Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. [En savoir plus sur les états de quarantaine](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitations du connecteur

Les jetons SCIM générés par Snowflake expirent au bout de 6 mois. N’oubliez pas que vous devez actualiser ces jetons avant leur expiration pour permettre aux synchronisations de provisionnement de continuer de fonctionner. 

## <a name="troubleshooting-tips"></a>Conseils de dépannage

Le service de provisionnement Azure AD fonctionne actuellement dans des [plages IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges) spécifiques. Si nécessaire, vous pouvez restreindre d’autres plages IP et ajouter ces plages IP particulières à la liste verte de votre application. Cette technique autorisera le flux de trafic du service de provisionnement Azure AD à votre application.

## <a name="change-log"></a>Journal des modifications

* 21/07/2020 : Activation de la suppression réversible pour tous les utilisateurs (via l’attribut actif).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Que sont l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
