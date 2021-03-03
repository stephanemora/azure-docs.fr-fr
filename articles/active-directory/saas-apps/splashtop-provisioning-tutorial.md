---
title: 'Tutoriel : Configurer Splashtop pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Splashtop.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 8d8c3745-aaa9-4dbd-9fbf-92da4ada2a9e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2021
ms.author: Zhchia
ms.openlocfilehash: af76b051350c896d5de3d2827de1f51ef3191f1a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652655"
---
# <a name="tutorial-configure-splashtop-for-automatic-user-provisioning"></a>Tutoriel : Configurer Splashtop pour l’attribution automatique d’utilisateurs

Ce tutoriel décrit les étapes à effectuer dans Splashtop et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois celui-ci configuré, Azure AD approvisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [Splashtop](https://www.splashtop.com/) à l’aide du service Approvisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Splashtop
> * Supprimer des utilisateurs dans Splashtop quand ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Splashtop
> * Approvisionner des groupes et des appartenances aux groupes dans Splashtop
> * [Authentification unique](./splashtop-tutorial.md) auprès de Splashtop (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Une équipe Splashtop avec prise en charge de l’authentification unique. Remplissez ce [formulaire de contact](https://marketing.splashtop.com/acton/fs/blocks/showLandingPage/a/3744/p/p-0095/t/page/fm/0) pour tester la fonctionnalité d’authentification unique et s’y abonner.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Splashtop](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-splashtop-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Splashtop pour prendre en charge l’approvisionnement avec Azure AD

1. Appliquez une nouvelle [méthode d’authentification unique](https://support-splashtopbusiness.splashtop.com/hc/articles/360038280751-How-to-apply-for-a-new-SSO-method-) sur le portail web de Splashtop.
2. Sur le portail web de Splashtop, générez le [jeton d’API](https://support-splashtopbusiness.splashtop.com/hc/articles/360046055352-How-to-generate-the-SCIM-provisioning-token-) pour configurer l’approvisionnement dans Azure AD.

## <a name="step-3-add-splashtop-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Splashtop à partir de la galerie d’applications Azure AD

Ajoutez Splashtop à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement pour Splashtop. Si vous avez déjà configuré Splashtop pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur et du groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à Splashtop, vous devez sélectionner un rôle différent du rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle Accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter d’autres rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-splashtop"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs sur Splashtop 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et des groupes dans TestApp en fonction des affectations d’utilisateurs et de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-splashtop-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Splashtop dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Splashtop**.

    ![Lien Splashtop dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’URL du locataire et le jeton secret de votre compte Splashtop. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Splashtop. Si la connexion échoue, vérifiez que votre compte Splashtop dispose d’autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Splashtop**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Splashtop. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Splashtop pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Splashtop prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:Splashtop:2.0:User:ssoName|String|
   

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Splashtop**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Splashtop. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Splashtop pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |membres|Informations de référence|

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour Splashtop, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et les groupes que vous voulez approvisionner sur Splashtop en choisissant les valeurs souhaitées dans **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

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