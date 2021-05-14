---
title: 'Tutoriel : Configurer Olfeo SAAS pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Apprenez à attribuer des comptes d’utilisateur et à supprimer leur privilèges d’accès automatiquement entre Azure AD et Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: 0afd2e2420868e40847ff1243fc61fb5b15c4853
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144081"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Tutoriel : Configurer Olfeo SAAS pour l’attribution automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Olfeo SAAS et Azure Active Directory (Azure AD) pour configurer l’attribution automatique d’utilisateurs. Une fois configuré, Azure AD attribue des utilisateurs et des groupes et supprime leurs privilèges d’accès automatiquement pour [Olfeo SAAS](https://www.olfeo.com) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Olfeo SAAS
> * Supprimer des utilisateurs dans Olfeo SAAS quand ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Olfeo SAAS
> * Approvisionner des groupes et des appartenances aux groupes dans Olfeo SAAS
> * [Authentification unique](olfeo-saas-tutorial.md) sur Olfeo SAAS (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un [locataire Olfeo SAAS](https://www.olfeo.com/).
* Un compte d’utilisateur dans Olfeo SAAS avec des autorisations d’administrateur.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Olfeo SAAS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Olfeo SAAS pour prendre en charge l’approvisionnement avec Azure AD

1. Connectez-vous à la console d’administration d’Olfeo SAAS. 
1. Accédez à **Configuration > Annuaires**.
1. Créez un nouveau répertoire, puis donnez-lui un nom.
1. Sélectionnez le fournisseur **Azure**, puis cliquez sur **Créer** pour enregistrer le nouveau répertoire. 
1. Accédez à l’onglet **Synchronisation** pour voir l’**URL du locataire** et le **Jeton secret**. Ces valeurs doivent être copiées et collées dans les champs **URL du locataire** et **Jeton secret** de l’onglet Approvisionnement, dans votre application Olfeo SAAS sur le portail Azure.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Olfeo SAAS à partir de la galerie d’applications Azure AD

Ajoutez Olfeo SAAS à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement auprès d’Olfeo SAAS. Si vous avez déjà configuré Olfeo SAAS pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à Olfeo SAAS, vous devez sélectionner un rôle autre que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Étape 5. Configurer l’attribution automatique d’utilisateurs sur Olfeo SAAS 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et des groupes dans Olfeo SAAS en fonction des attributions d’utilisateur et de groupe dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour Olfeo SAAS dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Olfeo SAAS**.

    ![Lien Olfeo SAAS dans la liste Applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1.  Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification d’administration**, entrez vos informations **URL du locataire** et **Jeton secret** issues d’Olfeo SAAS. Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Olfeo SAAS. Si la connexion échoue, vérifiez que votre compte Olfeo SAAS dispose d’autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Olfeo SAAS**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Olfeo SAAS. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Olfeo SAAS dans le cadre des opérations de mise à jour. Si vous modifiez l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Olfeo SAAS prend en charge le filtrage des utilisateurs en fonction de cet attribut. Sélectionnez **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |displayName|String|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|  

1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Olfeo SAAS**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Olfeo SAAS. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans Olfeo SAAS dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |membres|Informations de référence|

1. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service d’approvisionnement d’Azure AD pour Olfeo SAAS, dans la section **Paramètres**, définissez **État de l’approvisionnement** sur **Activé**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et les groupes à approvisionner dans Olfeo SAAS en sélectionnant les valeurs souhaitées sous **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle initial prend plus de temps que les suivants, qui se produisent toutes les 40 minutes environ tant que le service d’approvisionnement d’Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement

Après avoir configuré l’approvisionnement, utilisez les ressources suivantes pour superviser votre déploiement :

* Utilisez les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer pour quels utilisateurs le provisionnement a réussi et pour quels utilisateurs il a échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et sa progression.
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour plus d’informations sur les états de quarantaine, consultez [Provisionnement d’application en état de quarantaine](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)