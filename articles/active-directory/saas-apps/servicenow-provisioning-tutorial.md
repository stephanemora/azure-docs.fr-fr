---
title: 'Tutoriel : Configurer ServiceNow pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: jeedes
ms.openlocfilehash: afad903734fded062681f18683cb74492993fe1c
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109684214"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutoriel : Configurer ServiceNow pour l'approvisionnement automatique d'utilisateurs

Ce tutoriel décrit les étapes que vous effectuez dans ServiceNow et dans Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois Azure AD configuré, il provisionne et déprovisionne automatiquement les utilisateurs et les groupes pour [ServiceNow](https://www.servicenow.com/) à l’aide du service de provisionnement Azure AD. 

Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans ServiceNow
> * Supprimer des utilisateurs dans ServiceNow quand ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et ServiceNow
> * Approvisionner des groupes et des appartenances aux groupes dans ServiceNow
> * Autoriser l’[authentification unique](servicenow-tutorial.md) auprès de ServiceNow (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Dans Azure AD, un compte d’utilisateur disposant de l’[autorisation](../roles/permissions-reference.md) de configurer le provisionnement (Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général)
* Une [instance ServiceNow](https://www.servicenow.com/) de Calgary ou version ultérieure
* Une [instance ServiceNow Express](https://www.servicenow.com/) d’Helsinki ou une version ultérieure
* Un compte d’utilisateur dans ServiceNow avec le rôle d’administrateur

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1 : Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Étape 2 : Configurer ServiceNow pour prendre en charge l’approvisionnement avec Azure AD

1. Identifiez le nom de votre instance ServiceNow. Vous pouvez trouver le nom de l’instance dans l’URL que vous utilisez pour accéder à ServiceNow. Dans l’exemple suivant, le nom de l’instance est **dev35214**.

   ![Capture d’écran montrant une instance ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Obtenez les informations d’identification d’un administrateur dans ServiceNow. Accédez au profil utilisateur dans ServiceNow, puis vérifiez que l’utilisateur dispose du rôle d’administrateur. 

   ![Capture d’écran montrant un rôle d’administrateur ServiceNow.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Étape 3 : Ajouter ServiceNow à partir de la galerie d’applications Azure AD

Ajoutez ServiceNow à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement sur ServiceNow. Si vous avez déjà configuré ServiceNow pour l’authentification unique (SSO), vous pouvez utiliser la même application. Toutefois, nous vous recommandons de créer une application distincte quand vous testez l’intégration. [En savoir plus sur l’ajout d’une application à partir de la galerie](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4 : Définir qui sera dans l’étendue pour l’approvisionnement 

Le service de provisionnement Azure AD vous permet de définir l’étendue des utilisateurs qui seront provisionnés en fonction de l’affectation à l’application, ou en fonction des attributs de l’utilisateur ou du groupe. Si vous choisissez de définir l’étendue des utilisateurs qui seront provisionnés pour votre application en fonction de l’attribution, vous pouvez utiliser les [étapes permettant d’affecter des utilisateurs et des groupes à l’application](../manage-apps/assign-user-or-group-access-portal.md). Si vous choisissez de définir l’étendue des utilisateurs qui seront provisionnés uniquement en fonction des attributs de l’utilisateur ou du groupe, vous pouvez [utiliser un filtre d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Gardez à l’esprit les conseils suivants :

* Quand vous affectez des utilisateurs et des groupes à ServiceNow, vous devez sélectionner un autre rôle qu’Accès par défaut. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible sur l’application est le rôle Accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’attribution est définie sur les utilisateurs et les groupes affectés, vous pouvez contrôler cet aspect en affectant un ou deux utilisateurs ou groupes à l’application. Quand l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur les attributs](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Étape 5 : Configurer l’approvisionnement automatique d’utilisateurs sur ServiceNow 

Cette section vous guide tout au long des étapes à suivre pour configurer le service de provisionnement Azure AD afin de créer, de mettre à jour et de désactiver des utilisateurs et des groupes dans TestApp. Vous pouvez baser la configuration sur les affectations d’utilisateurs et de groupes dans Azure AD.

Pour configurer l'approvisionnement automatique d'utilisateurs pour ServiceNow dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Capture d’écran montrant le volet Applications d’entreprise.](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **ServiceNow**.

    ![Capture d’écran montrant une liste d’applications.](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de la liste déroulante Mode de provisionnement avec l’option Automatique en évidence.](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification d’administrateur**, entrez votre nom d’utilisateur et vos informations d’identification d’administrateur ServiceNow. Sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à ServiceNow. Si la connexion échoue, vérifiez que votre compte ServiceNow dispose d’autorisations d’administrateur, puis réessayez.

    ![Capture d’écran montrant la page Provisionnement du service, dans laquelle vous pouvez entrer des informations d’identification d’administrateur.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur de provisionnement. Cochez ensuite la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Capture d’écran montrant la zone et la case à cocher pour l’e-mail de notification.](common/provisioning-notification-email.png)

7. Sélectionnez **Save** (Enregistrer).

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec ServiceNow**.

9. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers ServiceNow dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans ServiceNow pour les opérations de mise à jour. 

   Si vous choisissez de changer l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez garantir que l’API ServiceNow prend en charge le filtrage des utilisateurs en fonction de cet attribut. 
   
   Cliquez sur le bouton **Enregistrer** pour valider les modifications.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec ServiceNow**.

11. Passez en revue les attributs de groupe qui sont synchronisés d’Azure AD vers ServiceNow dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans ServiceNow pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

12. Pour configurer des filtres d’étendue, consultez les instructions fournies dans le [tutoriel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour ServiceNow, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![Capture d’écran montrant le paramètre État du provisionnement activé.](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et les groupes que vous voulez provisionner sur ServiceNow en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Capture d’écran montrant les choix disponibles pour l’étendue du provisionnement.](common/provisioning-scope.png)

15. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Capture d’écran du bouton permettant d’enregistrer une configuration de provisionnement.](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle initial prend plus de temps que les cycles suivants. Les cycles suivants se produisent toutes les 40 minutes environ, tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6 : Surveiller votre déploiement
Après avoir configuré l’approvisionnement, utilisez les ressources suivantes pour superviser votre déploiement :

- Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou ceux pour laquelle la procédure a échoué.
- Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état d’avancement du cycle d’approvisionnement et le moment où il se terminera.
- Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. [En savoir plus sur les états de quarantaine](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Conseils de dépannage
* Quand vous provisionnez certains attributs, (comme **Service** et **Emplacement**) dans ServiceNow, les valeurs doivent déjà exister dans une table de référence dans ServiceNow. Si ce n’est pas le cas, vous obtenez une erreur **InvalidLookupReference**. 

   Par exemple, vous pouvez avoir deux localisations (Seattle, Los Angeles) et trois services (Ventes, Finances, Marketing) dans une table spécifique dans ServiceNow. Si vous tentez de provisionner un utilisateur dont le service est « Ventes » et qui se trouve à « Seattle », cet utilisateur est provisionné correctement. Si vous tentez de provisionner un utilisateur dont le service est « Ventes » et qui se trouve à « LA », l’utilisateur n’est pas provisionné. La localisation « LA » doit être ajouté à la table de référence dans ServiceNow, ou l’attribut utilisateur dans Azure AD doit être mis à jour afin de correspondre au format dans ServiceNow. 
* Si une erreur **EntryJoiningPropertyValueIsMissing** s’affiche, passez en revue vos [mappages d’attributs](../app-provisioning/customize-application-attributes.md) pour identifier l’attribut correspondant. Cette valeur doit être présente sur l’utilisateur ou le groupe que vous tentez de provisionner. 
* Pour comprendre les conditions ou les limitations (par exemple, le format à utiliser pour spécifier un indicatif de pays pour un utilisateur), passez en revue l’[API SOAP de ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html).
* Par défaut, les demandes de provisionnement sont envoyées à https://{nom-de-votre-instance}.service-now.com/{nom-table}. Si vous avez besoin d’une URL de locataire personnalisée, vous pouvez indiquer l’URL complète comme nom de l’instance.
* L’erreur **ServiceNowInstanceInvalid** indique un problème de communication avec l’instance ServiceNow. Voici le texte de l’erreur :
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Si vous rencontrez des problèmes de test de connexion, essayez de sélectionner **Non** pour les paramètres suivants dans ServiceNow :
   
  - **System Security** (Sécurité système) > **High security settings** (Paramètres de sécurité élevée) > **Require basic authentication for incoming SCHEMA requests** (Demander l’authentification de base pour les requêtes SCHEMA entrantes)
  - **System Properties** (Propriétés système) > **Web Services** (Services web) > **Require basic authorization for incoming SOAP requests** (Demander l’authentification de base pour les requêtes SOAP entrantes)

     ![Capture d’écran montrant l’option permettant d’autoriser les requêtes SOAP.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Si vous ne pouvez toujours pas résoudre votre problème, contactez les membres du support technique de ServiceNow et demandez-leur d’activer le débogage SOAP afin de faciliter le dépannage. 

* Le service de provisionnement Azure AD fonctionne actuellement dans des [plages d’adresses IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges) spécifiques. Si nécessaire, vous pouvez restreindre d’autres plages IP et ajouter ces plages IP particulières à la liste verte de votre application. Cette technique autorisera le flux de trafic du service de provisionnement Azure AD à votre application.

* Les instances de ServiceNow auto-hébergées ne sont pas prises en charge. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Que sont l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
