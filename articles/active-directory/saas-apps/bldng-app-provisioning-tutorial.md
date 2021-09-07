---
title: 'Tutoriel : Configurer BLDNG APP pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers BLDNG APP.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 5ccc1176-c244-4003-8486-67586bcdf317
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: thwimmer
ms.openlocfilehash: f9b8c1501ecb3aba941e412ca1f0583a6df23f38
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970484"
---
# <a name="tutorial-configure-bldng-app-for-automatic-user-provisioning-in-bldngai"></a>Tutoriel : Configurer BLDNG APP pour l’attribution automatique d’utilisateurs dans BLDNG.AI

Ce tutoriel décrit les étapes à suivre dans BLDNG APP et Azure Active Directory (Azure AD) pour configurer l’attribution automatique d’utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes pour [BLDNG APP](https://dashboard.bldng.ai/) avec le service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans BLDNG.AI 
> * Supprimer des utilisateurs dans BLDNG.AI quand ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et BLDNG.AI
> * Provisionner des groupes et des appartenances aux groupes dans BLDNG.AI
> * [Authentification unique](../manage-apps/add-application-portal-setup-oidc-sso.md) auprès de BLDNG.AI (recommandée)


## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un contrat [BLDNG.AI](https://dashboard.bldng.ai/)
* Une invitation de BLDNG.AI à activer l’attribution d’utilisateurs et à utiliser l’application BLDNG APP

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et BLDNG APP](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-bldng-app-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer BLDNG APP pour prendre en charge le provisionnement avec Azure AD

* Pour configurer l’attribution d’utilisateurs, de groupes d’utilisateurs et d’appartenances de groupe à partir d’Azure, vous avez besoin d’un contrat et d’un locataire BLDNG.AI.
* Pour obtenir un contrat, veuillez contacter un représentant du [service commercial](mailto:salg@bldng.ai). Vous ne pourrez pas continuer ni utiliser l’application BLDNG si aucun contrat n’existe.
* Si vous disposez déjà d’un contrat actif, mais que vous devez activer l’attribution d’utilisateurs uniquement, contactez directement le [support](mailto:support@bldng.ai).

Quand un contrat aura été établi, vous recevrez un e-mail contenant des instructions détaillées sur la configuration de l’attribution d’utilisateurs. L’e-mail inclut également les détails relatifs au consentement administrateur (au nom de votre organisation) pour l’utilisation de BLDNG APP, si nécessaire. 

L’e-mail inclut également l’URL du locataire et le jeton secret à utiliser lors de la configuration de l’attribution automatique d’utilisateurs. 

## <a name="step-3-add-bldng-app-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter BLDNG APP à partir de la galerie d’applications Azure AD

Ajoutez BLDNG APP à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement auprès de BLDNG APP. Si vous avez déjà configuré BLDNG APP pour l’authentification unique (SSO), vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à BLDNG APP, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-bldng-app"></a>Étape 5. Configurer l’attribution automatique d’utilisateurs sur BLDNG APP 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans BLDNG APP en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bldng-app-in-azure-ad"></a>Si vous souhaitez configurer l’attribution automatique d’utilisateurs pour BLDNG APP dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **BLDNG APP**.

    ![Lien BLDNG APP dans la liste Applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Sous la section **Informations d’identification de l’administrateur**, tapez l’**URL du locataire** BLDNG APP et le **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à BLDNG APP. Si la connexion échoue, vérifiez que votre compte BLDNG APP dispose des autorisations Administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec BLDNG APP**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et BLDNG APP. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans BLDNG APP pour les opérations de mise à jour. Si vous choisissez de changer l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API BLDNG APP prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

> [!NOTE]
> Il est important de noter que si vous changez le mappage de **externalId**, les utilisateurs de votre locataire ne sont pas en mesure de se connecter avec BLDNG APP.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   

1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec BLDNG APP**.

1. Dans la section **Mappage des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et BLDNG APP. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés afin de faire correspondre les groupes dans BLDNG APP pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |membres|Informations de référence|
      |externalId|String|      

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement Azure AD pour BLDNG APP, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou les groupes que vous souhaitez provisionner sur BLDNG APP en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)