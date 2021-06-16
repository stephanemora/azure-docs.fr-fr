---
title: "Tutoriel : Configurer Cisco Umbrella User Management pour l'approvisionnement automatique d'utilisateurs avec Azure Active Directory | Microsoft Docs"
description: Apprenez à procéder automatiquement à l'approvisionnement et au déprovisionnement de comptes d'utilisateur d'Azure AD vers Cisco Umbrella User Management.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 1aa20f40-19ec-4213-9a3b-5eb2bcdd9bbd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2021
ms.author: Zhchia
ms.openlocfilehash: becc5010908c810688cfbd969bb12f236b2f127f
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111896087"
---
# <a name="tutorial-configure-cisco-umbrella-user-management-for-automatic-user-provisioning"></a>Tutoriel : Configurer Cisco Umbrella User Management pour l'approvisionnement automatique d'utilisateurs

Ce tutoriel décrit les étapes à suivre dans Cisco Umbrella User Management et Azure Active Directory (Azure AD) pour configurer l'approvisionnement automatique d'utilisateurs. Une fois la configuration effectuée, Azure AD approvisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [Cisco Umbrella User Management](https://umbrella.cisco.com/) à l'aide du service d'approvisionnement d'Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Cisco Umbrella User Management
> * Supprimer des utilisateurs de Cisco Umbrella User Management lorsqu'ils n'ont plus besoin d'y accéder
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Cisco Umbrella User Management
> * Approvisionner des groupes et des appartenances à des groupes dans Cisco Umbrella User Management

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général). 
* Un [abonnement à Cisco Umbrella](https://signup.umbrella.com).
* Un compte d'utilisateur dans Cisco Umbrella avec autorisations d'administrateur complètes.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Cisco Umbrella User Management](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-import-objectguid-attribute-via-azure-ad-connect-optional"></a>Étape 2. Importer l'attribut ObjectGUID via Azure AD Connect (facultatif)
Si vous avez précédemment approvisionné des identités d'utilisateurs et de groupes de l'instance locale d'AD vers Cisco Umbrella et que vous souhaitez approvisionner les mêmes utilisateurs et groupes à partir d'Azure AD, vous devez synchroniser l'attribut ObjectGUID afin que les identités précédemment approvisionnées persistent dans la stratégie Umbrella.

> [!NOTE]
> Le connecteur AD Umbrella local doit être désactivé avant d'importer l'attribut ObjectGUID.
  
Lors de l'utilisation de Microsoft Azure AD Connect, l'attribut ObjectGUID des utilisateurs et des groupes n'est pas synchronisé par défaut entre l'instance locale d'AD et Azure AD. Pour synchroniser cet attribut, activez la **Synchronisation des attributs des extensions d'annuaire** (option facultative) et sélectionnez les attributs objectGUID des groupes et des utilisateurs.  

   ![Page Fonctionnalités facultatives de l’Assistant Azure Active Directory Connect](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extension-attribute-sync.png)

> [!NOTE]
> La recherche **Attributs disponibles** respecte la casse.

   ![Capture d’écran montrant la page de sélection « Extensions d’annuaire »](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extensions.png)

## <a name="step-3-configure-cisco-umbrella-user-management-to-support-provisioning-with-azure-ad"></a>Étape 3. Configurer Cisco Umbrella User Management pour prendre en charge l'approvisionnement avec Azure AD

1. Connectez-vous au [tableau de bord de Cisco Umbrella](https://login.umbrella.com ). Accédez à **Déploiements** > **Identités principales** > **Utilisateurs et groupes**.

2. Si le mécanisme d'importation est défini sur Importation manuelle, cliquez sur **Importer à partir d'un fournisseur d'identité** pour changer de mécanisme d'importation. 
  
3. Développez la carte Azure Active Directory et cliquez sur la **page Clés API**.

   ![API](./media/cisco-umbrella-user-management-provisioning-tutorial/keys.png)

4. Développez la carte Azure Active Directory sur la page Clés API, puis cliquez sur **Générer un jeton**.

   ![Generate](./media/cisco-umbrella-user-management-provisioning-tutorial/token.png)

5. Le jeton généré ne s'affiche qu'une seule fois. Copiez et enregistrez l'URL et le jeton. Ces valeurs doivent être respectivement entrées dans les champs **URL de locataire** et **Jeton secret** de l'onglet Approvisionnement de votre application Cisco Umbrella User Management sur le portail Azure.  


## <a name="step-4-add-cisco-umbrella-user-management-from-the-azure-ad-application-gallery"></a>Étape 4. Ajouter Cisco Umbrella User Management à partir de la galerie d'applications d'Azure AD

Ajoutez Cisco Umbrella User Management à partir de la galerie d'applications d'Azure AD pour commencer à gérer l'approvisionnement dans Cisco Umbrella User Management. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-5-define-who-will-be-in-scope-for-provisioning"></a>Étape 5. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous attribuez des utilisateurs et des groupes à Cisco Umbrella User Management, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-6-configure-automatic-user-provisioning-to-cisco-umbrella-user-management"></a>Étape 6. Configurer l'approvisionnement automatique d'utilisateurs pour Cisco Umbrella User Management 

Cette section vous guide tout au long des étapes de configuration du service d'approvisionnement d'Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Cisco Umbrella User Management en fonction des attributions d'utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-umbrella-user-management-in-azure-ad"></a>Pour configurer l'approvisionnement automatique d'utilisateurs pour Cisco Umbrella User Management dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Cisco Umbrella User Management**.

    ![Lien Cisco Umbrella User Management dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d'identification de l'administrateur**, saisissez l'URL du locataire Cisco Umbrella User Management et le jeton secret. Cliquez sur **Tester la connexion** pour vérifier qu'Azure AD peut se connecter à Cisco Umbrella User Management. Si la connexion échoue, vérifiez que votre compte Cisco Umbrella User Management dispose des autorisations d'administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Cisco Umbrella User Management**.

9. Dans la section **Mappages d'attributs**, passez en revue les attributs des utilisateurs qui sont synchronisés entre Azure AD et Cisco Umbrella User Management. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés dans le but de faire correspondre les comptes d'utilisateur dans Cisco Umbrella User Management pour les opérations de mise à jour. Si vous choisissez de changer l'[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l'API Cisco Umbrella User Management prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |active|Boolean|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId|String|

> [!NOTE]
> Si vous avez importé l'attribut objectGUID relatif aux utilisateurs via Azure AD Connect (voir l'étape 2), ajoutez un mappage entre objectGUID et urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Cisco Umbrella User Management**.

11. Dans la section **Mappages d'attributs**, passez en revue les attributs des groupes qui sont synchronisés entre Azure AD et Cisco Umbrella User Management. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés dans le but de faire correspondre les groupes dans Cisco Umbrella User Management pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |membres|Informations de référence|
      |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:Group:nativeObjectId|String|

> [!NOTE]
> Si vous avez importé l'attribut objectGUID relatif aux groupes via Azure AD Connect (voir l'étape 2), ajoutez un mappage entre objectGUID et urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId.

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d'approvisionnement d'Azure AD pour Cisco Umbrella User Management, dans la section **Paramètres**, définissez le paramètre **État d'approvisionnement** sur **Activé**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner dans Cisco Umbrella User Management en choisissant les valeurs souhaitées sous **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-7-monitor-your-deployment"></a>Étape 7. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitations du connecteur
* Cisco Umbrella User Management prend en charge l'approvisionnement d'un maximum de 200 groupes. Au-delà, les groupes figurant dans l'étendue ne peuvent pas être approvisionnés dans Cisco Umbrella. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)