---
title: 'Didacticiel : configurer Britive pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers Britive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225653"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Didacticiel : configurer Britive pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Britive et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et déprovisionne automatiquement des utilisateurs et des groupes pour [Britive](https://www.britive.com/) à l’aide du service Approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Britive
> * Supprimer les utilisateurs dans Britive quand ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Britive
> * Approvisionner des groupes et des appartenances aux groupes dans Britive
> * [Authentification unique](britive-tutorial.md) auprès de Britive (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un locataire [Britive](https://www.britive.com/).
* Un compte d’utilisateur dans Britive avec des autorisations d’administration.


## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Britive](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Britive pour prendre en charge l’approvisionnement avec Azure AD

L’application doit être configurée manuellement à l’aide de la procédure indiquée ci-dessous :
1. Connectez-vous à l’application Britive avec des privilèges d’administrateur
1. Cliquez sur **Admin -> Administration des utilisateurs -> Fournisseurs d'identité**
1. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité). Entrez un nom et une description. Cliquez sur le bouton Ajouter un fournisseur d’identité.

    ![Fournisseur d’identité](media/britive-provisioning-tutorial/identity.png)

1. Une page de configuration semblable à celle présentée ci-dessous s’affiche.

    ![Page de configuration](media/britive-provisioning-tutorial/configuration.png)

1. Cliquez sur l’onglet **SCIM**. Remplacez le fournisseur SCIM Generic par Azure, puis enregistrez les modifications. Copiez l’URL SCIM et notez-la. Ces valeurs seront entrées dans les zones **URL de locataire** de l’onglet Approvisionnement de votre application Britive sur le portail Azure.

    ![Page SCIM](media/britive-provisioning-tutorial/scim.png)

1. Cliquez sur **Créer un jeton**. Sélectionnez la validité du jeton selon vos besoins, puis cliquez sur le bouton Créer un jeton.

    ![Créer un jeton](media/britive-provisioning-tutorial/create-token.png)

1. Copiez le jeton généré et notez-le. Cliquez sur OK. Notez que l’utilisateur ne sera plus en mesure de voir le jeton. Cliquez sur le bouton Recréer pour générer un nouveau jeton si nécessaire. Ces valeurs seront entrées dans les zones **Jeton secret** et URL de locataire de l’onglet Approvisionnement de votre application getAbstract sur le portail Azure.

    ![Copie du jeton](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Britive à partir de la galerie d’applications Azure AD

Ajoutez Britive à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement pour Britive. Si vous avez déjà configuré Britive pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à Britive, vous devez sélectionner un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs sur Britive 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Britive en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Britive dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Britive**.

    ![Lien Britive dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Sous la section **Informations d’identification de l’administrateur**, entrez l’URL de locataire et le jeton secret de votre compte Britive. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Britive. Si la connexion échoue, vérifiez que votre compte Britive dispose des autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Britive**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Britive. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Britive pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Britive prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|
   |displayName|String|
   |title|String|
   |externalId|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |nickName|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |emails[type eq "work"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "home"].formatted|String|
   |addresses[type eq "home"].streetAddress|String|
   |addresses[type eq "home"].locality|String|
   |addresses[type eq "home"].region|String|
   |addresses[type eq "home"].postalCode|String|
   |addresses[type eq "home"].country|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "other"].streetAddress|String|
   |addresses[type eq "other"].locality|String|
   |addresses[type eq "other"].region|String|
   |addresses[type eq "other"].postalCode|String|
   |addresses[type eq "other"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informations de référence|


1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Britive**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Britive. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans Britive dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |externalId|String|
      |membres|Informations de référence|

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service d’approvisionnement Azure AD pour Britive, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner sur Britive en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)