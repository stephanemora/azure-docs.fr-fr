---
title: 'Tutoriel : Configurer Freshservice Provisioning pour le provisionnement automatique des utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes utilisateur d’Azure AD vers Freshservice Provisioning.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e03ec65a-25ef-4c91-a364-36b2f007443c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: thwimmer
ms.openlocfilehash: 2f5ee2257652bdfa07081a558a8723c334c11ed9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326910"
---
# <a name="tutorial-configure-freshservice-provisioning-for-automatic-user-provisioning"></a>Tutoriel : Configurer Freshservice Provisioning pour le provisionnement automatique des utilisateurs

Ce tutoriel décrit les étapes à effectuer dans Freshservice Provisioning et dans Azure Active Directory (Azure AD) pour configurer le provisionnement automatique des utilisateurs. Une fois qu’il est configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs dans [Freshservice Provisioning](https://effy.co.in/) à l’aide du service Provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Freshservice Provisioning
> * Supprimer des utilisateurs dans Freshservice Provisioning quand ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Freshservice Provisioning

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un [compte Freshservice](https://www.freshservice.com) avec les autorisations Administration d’organisation.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et Freshservice Provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-freshservice-provisioning-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Freshservice Provisioning pour prendre en charge le provisionnement avec Azure AD

1. Dans votre compte Freshservice, installez l’application **Provisionnement Azure (SCIM)** à partir de la place de marché en accédant à **Admin Freshservice** > **Applications** > **Obtenir des applications**.
2. Dans l’écran de configuration, renseignez votre **Domaine Freshservice** (par exemple, `acme.freshservice.com`) et la **clé d’API Administration d’organisation**.
3. Cliquez sur **Continuer**.
4. Mettez en évidence et copiez le **jeton du porteur**. Vous devrez entrer cette valeur dans le champ **Jeton secret** sous l’onglet Provisionnement de votre application Freshservice Provisioning dans le portail Azure.
5. Cliquez sur **Installer** pour effectuer l’installation.
6. L’**URL du locataire** est `https://scim.freshservice.com/scim/v2`. Vous devez entrer cette valeur dans le champ **URL du locataire** sous l’onglet Provisionnement de votre application Freshservice Provisioning dans le portail Azure.

## <a name="step-3-add-freshservice-provisioning-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Freshservice Provisioning à partir de la galerie d’applications Azure AD

Ajoutez Freshservice Provisioning à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans Freshservice Provisioning. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service de provisionnement Azure AD vous permet de définir l’étendue des utilisateurs provisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur. Si vous choisissez de définir l’étendue du provisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs à l’application. Si vous choisissez de définir l’étendue du provisionnement en fonction uniquement des attributs de l’utilisateur, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quand vous attribuez des utilisateurs à Freshservice Provisioning, vous devez sélectionner un rôle différent du rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-freshservice-provisioning"></a>Étape 5. Configurer le provisionnement automatique des utilisateurs dans Freshservice Provisioning 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs dans Freshservice Provisioning en fonction des affectations d’utilisateurs dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-freshservice-provisioning-in-azure-ad"></a>Pour configurer le provisionnement automatique des utilisateurs pour Freshservice Provisioning dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Freshservice Provisioning**.

    ![Lien Freshservice Provisioning dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, saisissez l’URL du locataire et le jeton secret Freshservice Provisioning. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Freshservice Provisioning. Si la connexion échoue, vérifiez que votre compte Freshservice Provisioning dispose d’autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Freshservice Provisioning**.

9. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Freshservice Provisioning. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés afin de faire correspondre les comptes d’utilisateur dans Freshservice Provisioning pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vous devez vérifier que l’API Freshservice Provisioning prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |addresses[type eq "work"].formatted|String|
   |locale|String|
   |title|String|
   |timezone|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informations de référence|
   |urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:isAgent|String|

> [!NOTE]
> Vous pouvez ajouter des attributs d’extension personnalisés à votre schéma pour répondre aux besoins de votre application en suivant les étapes ci-dessous :
> * Sous Mappages, sélectionnez **Provisionner des utilisateurs Azure Active Directory**.
> * En bas de la page, sélectionnez **Afficher les options avancées**.
> * Sélectionnez **Modifier la liste d’attributs pour Freshservice**.
> * En bas de la liste d’attributs, entrez les informations relatives à l’attribut personnalisé dans les champs fournis. L’espace de noms urn de l’attribut personnalisé doit suivre le modèle illustré dans l’exemple ci-dessous. Le paramètre **CustomAttribute** peut être personnalisé selon les exigences de votre application ; par exemple : urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:**isAgent**.
> * Vous devez sélectionner le type de données approprié pour l’attribut personnalisé, puis cliquez sur **Enregistrer**.
> * Revenez à l’écran des mappages par défaut et cliquez sur **Ajouter un nouveau mappage**. Les attributs personnalisés s’affichent dans la liste déroulante **Attribut cible**.

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Si vous souhaitez activer le service de provisionnement Azure AD pour Freshservice Provisioning, définissez **État du provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs que vous voulez provisionner dans Freshservice Provisioning en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs définis sous **Étendue**, dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
