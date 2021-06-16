---
title: "Tutoriel : Configurer AuditBoard pour l'approvisionnement automatique d'utilisateurs avec Azure Active Directory | Microsoft Docs"
description: Apprenez à procéder automatiquement à l'approvisionnement et au déprovisionnement de comptes d'utilisateur d'Azure AD vers AuditBoard.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e6ab736b-2bb7-4a5a-9f01-67c33f0ff97d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2021
ms.author: Zhchia
ms.openlocfilehash: a5b31646044f61029f15a2636dd918aaaf4e67ea
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111956653"
---
# <a name="tutorial-configure-auditboard-for-automatic-user-provisioning"></a>Tutoriel : Configurer AuditBoard pour l'approvisionnement automatique d'utilisateurs

Ce tutoriel décrit les étapes à suivre dans AuditBoard et Azure Active Directory (Azure AD) pour configurer l'approvisionnement automatique d'utilisateurs. Une fois la configuration effectuée, Azure AD approvisionne et déprovisionne automatiquement les utilisateurs dans [AuditBoard](https://www.auditboard.com/) à l'aide du service d'appprovisionnement d'Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans AuditBoard
> * Supprimer des utilisateurs d'AuditBoard lorsqu'ils n'ont plus besoin d'y accéder
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et AuditBoard
> * [Authentification unique](./auditboard-tutorial.md) auprès d'AuditBoard (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un site AuditBoard (en direct).

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et AuditBoard](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-auditboard-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer AuditBoard pour prendre en charge l'approvisionnement avec Azure AD

1. Connectez-vous à AuditBoard. Accédez à **Paramètres** > **Utilisateurs et rôles** > **Sécurité** > **SCIM**.

2. Cliquez sur **Generate Token** (Générer un jeton). 

3. Enregistrez le **Jeton** et l'**URL de base SCIM**. Ces valeurs doivent être entrées dans les champs URL de locataire et Jeton secret de l'onglet Approvisionnement de votre application AuditBoard sur le portail Azure.

   > [!NOTE]
   > La génération d'un nouveau jeton invalidera le jeton précédent.

4. L'instance AuditBoard exige que les autorisations utilisateur suivantes soient définies sur le rôle utilisateur SCIM (administrateur système par défaut). Connectez-vous au support AuditBoard pour vérifier que les paramètres suivants sont correctement définis : `user:action.administer must be set to allow` et `user:action.edit must be set to allow`.


## <a name="step-3-add-auditboard-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter AuditBoard à partir de la galerie d'applications Azure AD

Ajoutez AuditBoard à partir de la galerie d'applications Azure AD afin de commencer à gérer l'approvisionnement pour AuditBoard. Si vous avez déjà configuré AuditBoard pour l'authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue du provisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous attribuez des utilisateurs à AuditBoard, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs à l’application. Lorsque l’étendue est définie sur tous les utilisateurs, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-auditboard"></a>Étape 5. Configurer l'approvisionnement automatique d'utilisateurs pour AuditBoard 

Cette section vous guide tout au long des étapes de configuration du service d'approvisionnement d'Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des attributions d'utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-auditboard-in-azure-ad"></a>Pour configurer l'approvisionnement automatique d'utilisateurs pour AuditBoard dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **AuditBoard**.

    ![Lien AuditBoard dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Provisionnement automatique](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d'identification de l'administrateur**, saisissez l'URL du locataire AuditBoard et le jeton secret. Cliquez sur **Tester la connexion** pour vérifier qu'Azure AD peut se connecter à AuditBoard. Si la connexion échoue, vérifiez que votre compte AuditBoard dispose des autorisations d'administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Sous la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec AuditBoard**.

9. Sous la section **Mappages d'attributs**, passez en revue les attributs utilisateur synchronisés entre Azure AD et AuditBoard. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont dans le but de faire correspondre les comptes d'utilisateur dans AuditBoard pour les opérations de mise à jour. Si vous choisissez de changer l'[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l'API AuditBoard prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |emails[type eq "work"].value|String|&check;|
   |active|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d'approvisionnement Azure AD pour AuditBoard, définissez le paramètre **État d'approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs à attribuer à AuditBoard en choisissant les valeurs souhaitées sous **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs définis sous **Étendue**, dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)