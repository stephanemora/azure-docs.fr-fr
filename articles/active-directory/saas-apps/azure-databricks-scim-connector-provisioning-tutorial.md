---
title: "Tutoriel : Configurer Azure Databricks SCIM Connector pour l'approvisionnement automatique d'utilisateurs avec Azure Active Directory | Microsoft Docs"
description: Apprenez à approvisionner et déprovisionner automatiquement des comptes d'utilisateur entre Azure AD et Azure Databricks SCIM Connector.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370525"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Tutoriel : Configurer Azure Databricks SCIM Connector pour l'approvisionnement automatique d'utilisateurs

Ce tutoriel décrit les étapes à suivre dans Azure Databricks SCIM Connector et Azure Active Directory (Azure AD) pour configurer l'approvisionnement automatique d'utilisateurs. Une fois configuré, Azure AD approvisionne et déprovisionne automatiquement les utilisateurs et les groupes sur [Azure Databricks SCIM Connector](https://databricks.com/) à l'aide du service d'approvisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Azure Databricks SCIM Connector
> * Supprimer des utilisateurs dans Azure Databricks SCIM Connector lorsqu'ils n'ont plus besoin d'accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Azure Databricks SCIM Connector
> * Approvisionner des groupes et des appartenances à des groupes dans Azure Databricks SCIM Connector

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général). 
* Un compte Azure Databricks disposant d'autorisations d'administration.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et Azure Databricks SCIM Connector](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Azure Databricks SCIM Connector pour prendre en charge l'approvisionnement avec Azure AD

1. Pour configurer l'approvisionnement Azure Databricks SCIM, ajoutez-le en tant que ressource dans votre locataire Azure Active Directory et configurez-le à l'aide des paramètres ci-dessous.

    ![Configuration d'Azure Databricks](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Pour générer un jeton d'accès personnel dans Azure Databricks, consultez [ce document](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management).

3. Copiez le **jeton**. Cette valeur sera entrée dans le champ Jeton secret de l'onglet Approvisionnement de votre application Azure Databricks SCIM Connector sur le portail Azure.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Azure Databricks SCIM Connector à partir de la galerie d'applications Azure AD

Ajoutez Azure Databricks SCIM Connector à partir de la galerie d'applications Azure AD pour commencer à gérer l'approvisionnement vers Azure Databricks SCIM Connector. Si vous avez déjà configuré Azure Databricks SCIM Connector pour l'authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Lors de l'attribution d'utilisateurs et de groupes à Azure Databricks SCIM Connector, vous devez sélectionner un rôle différent du rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Étape 5. Configurer l'approvisionnement automatique d'utilisateurs sur Azure Databricks SCIM Connector 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!NOTE]
> Pour en savoir plus sur le point de terminaison SCIM d'Azure Databricks, consultez [ce document](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Pour configurer l'approvisionnement automatique d'utilisateurs sur Azure Databricks SCIM Connector dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Azure Databricks SCIM Connector**.

    ![Lien Azure Databricks SCIM Connector dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez la valeur de point de terminaison SCIM dans **URL de locataire**. L’URL du locataire doit être au format `https://<region>.azuredatabricks.net/api/2.0/preview/scim` où la **région** peut être trouvée dans votre URL de page d’accueil Azure Databricks. Par exemple, un point de terminaison SCIM pour la région **westus** sera `https://westus.azuredatabricks.net/api/2.0/preview/scim`. Saisissez la valeur de jeton récupérée précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu'Azure AD peut se connecter à Azure Databricks SCIM Connector. Si la connexion échoue, vérifiez que votre compte Azure Databricks SCIM Connector dispose des autorisations d'administrateur, puis réessayez.

    ![approvisionnement](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Azure Databricks SCIM Connector**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Azure Databricks SCIM Connector. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés afin de faire correspondre les comptes d'utilisateur dans Azure Databricks SCIM Connector pour les opérations de mise à jour. Si vous choisissez de modifier l'[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vous devez vérifier que l'API Azure Databricks SCIM Connector prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |active|Boolean|

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Azure Databricks SCIM Connector**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Azure Databricks SCIM Connector. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés afin de faire correspondre les groupes dans Azure Databricks SCIM Connector pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

     |Attribut|Type|
     |---|---|
     |displayName|String|
     |membres|Informations de référence|

11. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Azure Databricks SCIM Connector**.

12. Pour activer le service d'approvisionnement Azure AD pour Azure Databricks SCIM Connector, attribuez la valeur **Activé** au paramètre **État de l'approvisionnement** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

13. Définissez les utilisateurs et/ou groupes que vous souhaitez approvisionner sur Azure Databricks SCIM Connector en choisissant les valeurs souhaitées dans le champ **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

14. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Conseils de dépannage
* Les bases de données convertissent toujours les valeurs de leurs noms d'utilisateur en minuscules lors de l'enregistrement dans leur répertoire, quelle que soit le paramètre de mise en majuscules que nous leur envoyons via SCIM.
* Actuellement, en ce qui concerne les utilisateurs, les requêtes GET associées à l'API SCIM d'Azure Databricks sont sensibles à la casse. Par conséquent, si nous demandons USER@contoso.com, il n'y aura aucun résultat car l'utilisateur est stocké sous la forme user@contoso.com.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
