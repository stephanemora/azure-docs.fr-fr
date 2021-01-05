---
title: 'Tutoriel : Configurer Bizagi Studio for Digital Process Automation pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD à Bizagi Studio for Digital Process Automation.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 141dd37dff8403825df713de8f7176d4dd9d20f8
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008042"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Tutoriel : Configurer Bizagi Studio for Digital Process Automation pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre à la fois dans Bizagi Studio for Digital Process Automation et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois configuré, Azure AD attribue automatiquement les utilisateurs et les groupes (et annule leur attribution) dans [Bizagi Studio for Digital Process Automation](https://www.bizagi.com/) à l’aide du service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Création d’utilisateurs dans Bizagi Studio for Digital Process Automation
> * Suppression d’utilisateurs dans Bizagi Studio for Digital Process Automation quand ils n’ont plus besoin d’accès
> * Maintien de la synchronisation des attributs utilisateur entre Azure AD et Bizagi Studio for Digital Process Automation
> * [Authentification unique](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) auprès de Bizagi Studio for Digital Process Automation (recommandée)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). 
* Un compte d’utilisateur dans Azure AD disposant d’une [autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) pour configurer le provisionnement. Exemples : Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général. 
* Bizagi Studio for Digital Process Automation version 11.2.4.2X ou ultérieure.

## <a name="plan-your-provisioning-deployment"></a>Planifier votre déploiement de l’approvisionnement
Procédez comme suit pour la planification :

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera [concerné par le provisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et Bizagi Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Configuration pour prendre en charge le provisionnement avec Azure AD
Pour configurer Bizagi Studio for Digital Process Automation pour prendre en charge le provisionnement avec Azure AD, procédez comme suit :

1. Connectez-vous à votre portail professionnel en tant qu’utilisateur doté **d’autorisations d’administrateur**.

2. Accédez à **Administration** > **Sécurité** > **Applications OAuth 2**.

   ![Capture d’écran de Bizagi, avec Applications OAuth 2 encadrées.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Sélectionnez **Ajouter**.
4. Pour **Type d’autorisation**, sélectionnez **Jeton du porteur**. Pour **Étendue autorisée**, sélectionnez **API** et **SYNCHRONISATION UTILISATEUR**. Ensuite, sélectionnez **Enregistrer**.

   ![Capture d’écran de l’application de Registre, avec Type d’autorisation et Étendue autorisée encadrés.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Copiez et enregistrez le **Secret client**. Sur le Portail Azure, la valeur de la clé secrète client est entrée dans le champ **Jeton secret** de l’onglet **Provisionnement** pour l’application Bizagi Studio for Digital Process Automation.

   ![Capture d’écran d’OAuth, avec Clé secrète client encadré.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Ajout de l’application à partir de la galerie Azure AD

Pour commencer à gérer le provisionnement dans Bizagi Studio for Digital Process Automation, ajoutez l’application à partir de la galerie d’applications Azure AD. Si vous avez déjà configuré Bizagi Studio for Digital Process Automation pour l’authentification unique, vous pouvez utiliser la même application. Pour le test initial de l’intégration toutefois, créez plutôt une application distincte. Pour plus d’informations, consultez [Démarrage rapide : Ajout d’une application à un locataire Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="define-who-is-in-scope-for-provisioning"></a>Définir qui est concerné par la configuration 

Avec le service de provisionnement Azure AD, vous pouvez définir l’étendue des utilisateurs attribués en fonction de l’affectation à l’application, en fonction des attributs de l’utilisateur et du groupe ou en fonction des deux. Si vous définissez l’étendue en fonction de l’affectation, consultez la procédure décrite dans [Affectation ou suppression de l’affectation des utilisateurs et des groupes pour une application avec l’API Graph](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous définissez l’étendue en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue. Pour plus d’informations, voir [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

Notez les points suivants concernant la définition de l’étendue :

* Quand vous affectez des utilisateurs et des groupes à Bizagi Studio for Digital Process Automation, vous devez sélectionner un rôle différent du rôle **Accès par défaut**. Les utilisateurs disposant du rôle d’accès par défaut sont exclus de l’attribution et marqués comme non expressément autorisés dans les journaux de provisionnement. Si le seul rôle disponible sur l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter plus de rôles. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’attribution est définie sur les utilisateurs et les groupes affectés, vous pouvez contrôler cet aspect en affectant un ou deux utilisateurs ou groupes à l’application. Quand l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur les attributs](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="configure-automatic-user-provisioning"></a>Configurer le provisionnement d’utilisateurs automatique 

Cette section vous guide tout au long de la procédure de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et des groupes. Vous effectuez cette tâche dans votre application de test, en fonction des affectations d’utilisateurs et de groupes dans Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Configuration de l’attribution automatique d’utilisateurs pour Bizagi Studio for Digital Process Automation dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Capture d’écran du Portail Azure, avec Applications d’entreprise et Toutes les applications encadrés.](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Bizagi Studio for Digital Process Automation**.

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer, avec Provisionnement encadré.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran du contrôle Mode de provisionnement, avec Automatique encadré.](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification d’administration**, entrez l’URL de votre locataire et le jeton secret de Bizagi Studio for Digital Process Automation. 

      * **URL de locataire :** entrez le point de terminaison Bizagi SCIM, avec la structure suivante : `<Your_Bizagi_Project>/scim/v2/`.
         Par exemple : `https://my-company.bizagi.com/scim/v2/`.

      * **Jeton secret :** cette valeur est extraite de l’étape décrite précédemment dans cet article.

      Pour vérifier qu’Azure AD peut se connecter à Bizagi Studio for Digital Process Automation, sélectionnez **Tester la connexion**. Si la connexion échoue, veillez à ce que votre compte Bizagi Studio for Digital Process Automation dispose des autorisations d’administrateur, puis réessayez.

   ![Capture d’écran d’Informations d’identification d’administration, avec Tester la connexion encadré.](common/provisioning-testconnection-tenanturltoken.png)

6. Pour **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement. Sélectionnez l’option **Envoyer une notification par e-mail en cas de défaillance**.

    ![Capture d’écran des options E-mail de notification.](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Bizagi Studio for Digital Process Automation**.

9. Dans la section **Mappages des attributs**, vérifiez les attributs d’utilisateurs synchronisés entre Azure AD et Bizagi Studio for Digital Process Automation. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Bizagi Studio for Digital Process Automation pour les opérations de mise à jour. Si vous modifiez [l’attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vérifiez que l’API Bizagi Studio for Digital Process Automation prend en charge le filtrage des utilisateurs en fonction de cet attribut. Sélectionnez **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|
   
10. Pour configurer des filtres d’étendue, consultez le [tutoriel sur les filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service de provisionnement Azure AD pour Bizagi Studio for Digital Process Automation, affectez la valeur **Activé** au paramètre **État de provisionnement** dans la section **Paramètres**.

    ![Capture d’écran du bouton bascule État de provisionnement.](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et les groupes que vous souhaitez attribuer dans Bizagi Studio for Digital Process Automation. Dans la section **Paramètres**, choisissez les valeurs souhaitées dans **Étendue**.

    ![Capture d’écran des options Étendue.](common/provisioning-scope.png)

13. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Capture d’écran du contrôle Enregistrer.](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="monitor-your-deployment"></a>Surveiller votre déploiement
Après avoir configuré le provisionnement, utilisez les ressources suivantes pour superviser votre déploiement :

- Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou ceux pour laquelle la procédure a échoué.
- Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle de provisionnement et sa progression.
- Si la configuration du provisionnement semble se trouver dans un état non sain, l’application se met en quarantaine. Pour plus d’informations, consultez [Provisionnement d’applications en état de quarantaine](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
