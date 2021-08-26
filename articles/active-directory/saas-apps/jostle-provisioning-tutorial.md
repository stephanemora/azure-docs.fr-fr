---
title: 'Didacticiel : configurer Jostle pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers Jostle.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: thwimmer
ms.openlocfilehash: 5fe843fb735aecda1eff51d3e52392a79bf8c1dd
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207346"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Didacticiel : configurer Jostle pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans Jostle et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et déprovisionne automatiquement les utilisateurs et les groupes pour [Jostle](https://www.jostle.me/) à l’aide du service d’approvisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Jostle
> * Supprimer des utilisateurs dans Jostle lorsqu’ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Jostle
> * [Authentification unique](jostle-tutorial.md) auprès de Jostle (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un [locataire Jostle](https://www.jostle.me/).
* Un compte d’utilisateur dans Jostle avec des autorisations d’administrateur.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Jostle](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Étape 2. Configuration de Jostle pour prendre en charge le provisionnement avec Azure AD

### <a name="automation-account"></a>Compte Automation

Avant de commencer, vous devez créer un **utilisateur Automation** sur votre intranet Jostle. Il s’agit du compte que vous allez utiliser pour configurer avec Azure. Vous pouvez créer des utilisateurs Automation dans les **Paramètres d’administration > Comptes d’utilisateurs et données > Gérer les utilisateurs Automation**.

Pour plus d’informations sur les utilisateurs Automation et savoir comment en créer, consultez [cet article](https://forum.jostle.us/hc/en-us/articles/360057364073).

Une fois créé, le compte d’utilisateur Automation **doit être activé** (c’est-à-dire connecté à votre intranet au moins une fois) pour pouvoir être utilisé pour configurer Azure.

### <a name="manage-user-provisioning"></a>Gérer la configuration des utilisateurs

Avant de commencer, assurez-vous que votre abonnement à votre compte **comprend des fonctionnalités de configuration de l’authentification unique/d’utilisateur**. Si ce n’est pas le cas, vous pouvez contacter votre responsable de la réussite des clients <success@jostle.me> pour vous aider à les ajouter à votre compte.

L’étape suivante consiste à obtenir l’**URL de l’API** et la **clé API** à partir de Jostle :

1. Accédez à la navigation principale, puis cliquez sur **Paramètres d’administration**.
1. Sous **Données utilisateur vers/à partir d’autres systèmes**, cliquez sur **Gérer la configuration des utilisateurs**. Si vous ne voyez pas « Gérer la configuration des utilisateurs » ici et que vous avez vérifié que votre compte comprenait la configuration de l’authentification unique/d’utilisateur, contactez le support technique <support@jostle.me> pour activer cette page dans vos paramètres d’administration).
1. Dans la section **Détails de l’API de configuration d’utilisateur**, accédez au champ **Votre URL de base**, cliquez sur le bouton Copier et enregistrez l’URL à un endroit où vous pourrez facilement y accéder ultérieurement.                                                           

   ![Approvisionnement](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Ensuite, cliquez sur le bouton **Ajouter une nouvelle clé...**
1. Dans l’écran suivant, accédez au champ **Utilisateur Automation** et utilisez le menu déroulant pour sélectionner votre compte d’utilisateur Automation. 

   ![Integration Account](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. Dans le champ **Description de la clé d’API de configuration**, attribuez un nom à votre clé (par exemple, « Azure »), puis cliquez sur le bouton **Ajouter**.

1. Une fois votre clé générée, **veillez à la copier immédiatement** et à l’enregistrer à l’emplacement où vous avez enregistré votre URL (car il s’agit de la seule fois où votre clé s’affichera).                                                               
1. Ensuite, vous utiliserez l’**URL de l’API** et la **clé API** pour configurer l’intégration dans Azure.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Jostle à partir de la galerie d’applications Azure AD

Ajoutez Jostle à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement sur Jostle. Si vous avez déjà configuré Jostle pour l’authentification unique (SSO), vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous attribuez des utilisateurs et des groupes à Jostle, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs pour Jostle 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et des groupes dans Jostle en fonction des affectations d’utilisateurs et de groupes dans Azure AD.

> [!NOTE]
> Pour plus d’informations sur le provisionnement automatique d’utilisateurs vers Jostle, consultez [Provisionnement des utilisateurs : intégration Azure](https://forum.jostle.us/hc/en-us/articles/360056368534-User-Provisioning-Azure-Integration).

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Jostle dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Jostle**.

    ![Lien Jostle dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Provisionnement** et cliquez sur **prise en main**.

    ![Onglet Approvisionnement](common/provisioning.png)

1.  Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification d’administration**, entrez vos informations **URL du locataire** et **Jeton secret** issues de Jostle. Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Jostle. Si la connexion échoue, vérifiez que votre compte Jostle dispose d’autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**. Notez toutefois que Jostle envoie également des notifications d’échec de provisionnement. Cette option est donc facultative.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Provisionner les utilisateurs Azure Active Directory**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Jostle. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Jostle pour les opérations de mise à jour. Si vous modifiez l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Jostle prend en charge le filtrage des utilisateurs en fonction de cet attribut. Sélectionnez **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "personal"].value|String|
   |emails[type eq "alternate1"].value|String|
   |emails[type eq "alternate2"].value|String|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|String|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |String|  

1. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement Azure AD pour Jostle, définissez **État du provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs ou les groupes que vous souhaitez provisionner dans Jostle en sélectionnant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**. Pour Jostle, **l’Étendue** doit être définie sur « Synchroniser uniquement les utilisateurs et les groupes affectés ».

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