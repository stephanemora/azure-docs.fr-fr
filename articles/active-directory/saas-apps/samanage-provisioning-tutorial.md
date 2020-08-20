---
title: 'Tutoriel : Configurer Samanage pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Samanage.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 21a3c81d9a24cc63d3fc77c95c94df1e9113d292
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543446"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutoriel : Configurer Samanage pour le provisionnement automatique d’utilisateurs
Ce tutoriel décrit les étapes à effectuer dans Samanage et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois celui-ci configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [Samanage](https://www.samanage.com/pricing/) à l’aide du service Provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-samange-application"></a>Migrer vers la nouvelle application Samanage

Si vous disposez d’une intégration existante à Samanage, consultez la section ci-dessous concernant les modifications à venir. Si vous configurez Samanage pour la première fois, vous pouvez ignorer cette section et passer à la section **Fonctionnalités prises en charge**.

#### <a name="whats-changing"></a>Ce qui a changé
* Changements du côté Azure AD : La méthode d’autorisation pour provisionner des utilisateurs dans Samanage a été jusqu’à maintenant une **authentification de base**. Bientôt, la méthode d’autorisation utilisée sera remplacée par le **jeton secret à long terme**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Que dois-je faire pour migrer mon intégration personnalisée existante vers la nouvelle application ?
Si vous disposez d’une intégration Samanage existante avec des informations d’identification d’administrateur valides, **vous n’avez rien à faire**. Nous effectuerons automatiquement la migration des clients vers la nouvelle application. Ce processus aura lieu en arrière-plan. Si les informations d’identification existantes expirent, ou si vous avez besoin d’autoriser l’accès à l’application, vous devez générer un jeton secret à long terme. Pour générer un nouveau jeton, reportez-vous à l’étape 2 de cet article.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Comment savoir si mon application a été migrée ? 
Une fois votre application migrée, dans la section **Informations d’identification de l’administrateur**, les champs **Nom d’utilisateur de l’administrateur** et **Mot de passe de l’administrateur** sont remplacés par un seul champ intitulé **Jeton secret**.

## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Samanage
> * Supprimer des utilisateurs dans Samanage quand ils ne nécessitent plus d’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Samanage
> * Provisionner des groupes et des appartenances aux groupes dans Samanage
> * [Authentification unique](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) auprès de Samanage (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général). 
* Un [locataire Samanage](https://www.samanage.com/pricing/) avec le package Professionnel.
* Un compte d’utilisateur dans Samanage avec des autorisations d’administration.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et Samanage](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Samanage pour prendre en charge le provisionnement avec Azure AD

Pour savoir comment générer un jeton secret pour l’authentification, [cliquez ici](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Samanage à partir de la galerie d’applications Azure AD

Ajoutez Samanage à partir de la galerie d’applications Azure AD pour gérer le provisionnement dans Samanage. Si vous avez déjà configuré Samanage pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quand vous attribuez des utilisateurs et des groupes à Samanage, vous devez sélectionner un rôle autre que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs sur Samanage 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Samanage dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Samanage**.

    ![Lien Samanage dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez `https://api.samanage.com` dans **URL de locataire**.  Entrez la valeur du jeton secret récupérée précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Samanage. Si la connexion échoue, vérifiez que votre compte Samanage dispose d’autorisations d’administrateur, puis réessayez.

    ![approvisionnement](./media/samanage-provisioning-tutorial/provisioning.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Samanage**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs d’utilisateurs qui sont synchronisés entre Azure AD et Samanage. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vous devez vérifier que l’API Samanage prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      ![Mappages d’utilisateurs Samanage](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Samanage**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Samanage. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Samanage pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      ![Mappages de groupes Samanage](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour Samanage, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Samanage en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Limitations du connecteur

Si vous sélectionnez l’option **Synchroniser l’ensemble des utilisateurs et groupes** option et configurez une valeur pour l’attribut **roles** de Samanage, la valeur dans la zone **Valeur par défaut si Null (facultatif)** doit être exprimée dans le format suivant :

- {"displayName":"role"}, où rôle est la valeur par défaut souhaitée.

## <a name="change-log"></a>Journal des modifications

* 22/04/2020 - Mise à jour de la méthode d’autorisation : remplacement de l’authentification de base par le jeton secret à long terme.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
