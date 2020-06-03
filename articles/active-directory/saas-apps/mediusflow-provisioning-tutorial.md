---
title: 'Tutoriel : Configurer MediusFlow pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et annuler automatiquement l’approvisionnement des comptes d’utilisateur depuis Azure AD vers MediusFlow.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: c6c65170-c5cb-44f6-81f8-25d972759e2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: cd2217046a725f0bb0a05f1eb0afc5434bf3b6fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83662717"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Tutoriel : Configurer MediusFlow pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes que vous devez effectuer dans MediusFlow et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne les utilisateurs et les groupes et annule l’approvisionnement automatiquement pour [MediusFlow](https://www.mediusflow.com/) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans MediusFlow
> * Supprimer des utilisateurs dans MediusFlow lorsqu’ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et MediusFlow
> * Approvisionner des groupes et des appartenances aux groupes dans MediusFlow
> * Authentification unique dans MediusFlow (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un abonnement MediusFlow actif avec assurance qualité ou locataire de production.
* Un compte d’utilisateur dans MediusFlow avec des droits d’accès administrateur pour pouvoir effectuer la configuration dans MediusFlow.
* Les entreprises sont ajoutées au locataire MediusFlow à l’emplacement où les utilisateurs doivent être approvisionnés.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et MediusFlow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurez MediusFlow pour prendre en charge l’approvisionnement avec Azure AD

### <a name="activate-the-office-365-app-within-mediusflow"></a>Activez l’application Office 365 dans MediusFlow
Commencez par activer l’accès à la connexion Azure AD et la fonctionnalité de configuration Azure AD dans MediusFlow en procédant comme suit :

#### <a name="user-login"></a>Connexion utilisateur
Pour activer le flux de connexions à Office 365/Azure AD. Reportez-vous à l’article [ici] (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup).

#### <a name="user-transfer-configuration"></a>Configuration du transfert des utilisateurs
Pour activer le portail de configuration des utilisateurs pour l’approvisionnement à partir de Azure AD, consultez [cet](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) article.

#### <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

1.  Connectez-vous à la [console d’administration MediusFlow](https://office365.cloudapp.mediusflow.com/) en fournissant l’ID du locataire.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Vérifiez la connexion avec MediusFlow.

    ![Vérifier](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Spécifiez l’ID de votre locataire Azure AD.

    ![spécifiez l’ID du locataire](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Vous trouverez plus d’informations sur comment trouver l’ID du locataire dans notre [FAQ](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id).

4. Enregistrez la configuration.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Sélectionnez Approvisionnement d’utilisateurs et cliquez sur **OK**.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Cliquez sur **Générer une clé secrète**. Copiez et enregistrez cette valeur. Elle devra être entrée dans le champ **Jeton secret** de l’onglet **Approvisionnement** de votre application MediusFlow dans le portail Azure.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Cliquez sur **OK**.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. Pour que les utilisateurs soient importés avec un ensemble prédéfini de rôles, d’entreprises et autres configurations générales dans MediusFlow, vous devez d’abord le configurer. Commencez par ajouter la configuration en cliquant sur **Ajouter une nouvelle configuration**.

    ![Config utilisateurs](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Spécifiez les paramètres par défaut pour les utilisateurs. Dans cet affichage, il est possible de définir l’attribut par défaut. Si les paramètres standard sont bons, vous n’aurez qu’à fournir un nom d’entreprise valide. Comme ces paramètres de configuration sont extraits de MediusFlow, ils doivent être configurés en premier. Pour plus d’informations, consultez la section **Prérequis** de cet article.

    ![Config utilisateurs](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. Cliquez sur le bouton **Enregistrer** pour enregistrer la configuration utilisateur.

    ![Config utilisateurs](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. Pour accéder au lien d’approvisionnement d’utilisateur, cliquez sur **Copier le lien SCIM**. Copiez et enregistrez cette valeur. Cette valeur devra être entrée dans le champ **URL du locataire** dans l’onglet **Approvisionnement** de votre application MediusFlow dans le portail Azure.
 
    ![Config utilisateurs](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter MediusFlow à partir de la galerie d’applications Azure AD

Ajoutez MediusFlow à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement sur MediusFlow. Si vous avez déjà configuré MediusFlow pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Lorsque vous affectez des utilisateurs et des groupes à MediusFlow, vous devez sélectionner un autre rôle qu’**Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs à MediusFlow 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour MediusFlow dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **MediusFlow**.

    ![Lien MediusFlow dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d'identification Administrateur**, entrez la valeur de l'URL du locataire récupérée précédemment dans **URL du locataire**. Entrez la valeur du jeton secret récupérée précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à MediusFlow. Si la connexion échoue, vérifiez que votre compte MediusFlow dispose des autorisations administrateur et réessayez.

      ![approvisionnement](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur MediusFlow**.

9. Passez en revue les attributs utilisateur synchronisés d’Azure AD vers MediusFlow dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans MediusFlow pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vous devez vous assurer que l’API MediusFlow prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |name.displayName|String|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalID|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informations de référence|


10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec MediusFlow**.

11. Passez en revue les attributs de groupe synchronisés d’Azure AD vers MediusFlow dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans MediusFlow pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|
      |---|---|
      |displayName|String|
      |externalID|String|
      |membres|Informations de référence|

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour MediusFlow, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes à approvisionner sur MediusFlow en choisissant les valeurs souhaitées dans le champ **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
