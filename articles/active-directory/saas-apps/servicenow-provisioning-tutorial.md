---
title: 'Tutoriel : Configurer ServiceNow pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et annuler l’approvisionnement automatiquement des comptes utilisateur d’Azure AD vers ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 04fb34ff9c0ba9822e9760bcd3989bd0b5675506
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044735"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Tutoriel : Configurer ServiceNow pour l'approvisionnement automatique d'utilisateurs

Ce didacticiel décrit les étapes que vous devez effectuer dans ServiceNow et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et désapprovisionne automatiquement les utilisateurs et les groupes pour [ServiceNow](https://www.servicenow.com/) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans ServiceNow
> * Supprimer les utilisateurs dans ServiceNow lorsqu’ils ne nécessitent plus d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et ServiceNow
> * Approvisionner des groupes et des appartenances aux groupes dans ServiceNow
> * [Authentification unique](servicenow-tutorial.md) à ServiceNow (recommandé)

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général). 
* Une [instance ServiceNow](https://www.servicenow.com/) de Calgary ou version ultérieure
* Une [instance ServiceNow Express](https://www.servicenow.com/) d’Helsinki ou une version ultérieure
* Un compte d’utilisateur dans ServiceNow avec le rôle d’administrateur

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer ServiceNow pour prendre en charge l’approvisionnement avec Azure AD

1. Identifiez le nom de votre instance ServiceNow. Vous pouvez trouver le nom de l’instance dans l’URL que vous utilisez pour accéder à ServiceNow. Dans l’exemple ci-dessous, le nom de l’instance est dev35214.

![Instance ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Obtenez les informations d’identification d’un administrateur dans ServiceNow. Accédez au profil utilisateur dans ServiceNow et vérifiez que l’utilisateur a le rôle d’administrateur. 

![Rôle d’administrateur ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter ServiceNow à partir de la galerie d’applications Azure AD

Ajoutez ServiceNow à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement sur ServiceNow. Si vous avez déjà configuré ServiceNow pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Lorsque vous affectez des utilisateurs et des groupes à ServiceNow, vous devez sélectionner un autre rôle qu’**Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs sur ServiceNow 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Pour configurer l'approvisionnement automatique d'utilisateurs pour ServiceNow dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **ServiceNow**.

    ![Lien ServiceNow dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification d’administrateur**, entrez vos informations d’identification d’administrateur ServiceNow et votre nom d’utilisateur. Cliquez sur **Tester la connexion** pour vérifier qu'Azure AD peut se connecter à ServiceNow. Si la connexion échoue, vérifiez que votre compte ServiceNow dispose d’autorisations d’administrateur et réessayez.

    ![approvisionnement](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec ServiceNow**.

9. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers ServiceNow dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans ServiceNow pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vous devez vous assurer que l’API ServiceNow prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec ServiceNow**.

11. Passez en revue les attributs de groupe qui sont synchronisés d’Azure AD vers ServiceNow dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans ServiceNow pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour ServiceNow, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur ServiceNow en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Conseils de dépannage
* **InvalidLookupReference :** Lors de l’approvisionnement de certains attributs, tels que le service et l’emplacement dans ServiceNow, les valeurs doivent déjà exister dans une table de référence dans ServiceNow. Par exemple, vous pouvez avoir deux emplacements (Seattle, Los Angeles) et trois départements (ventes, finance, marketing) dans la table **insérer le nom de table** dans ServiceNow. Si vous tentez d’approvisionner un utilisateur dont le service est « Ventes » et où l’emplacement est « Seattle », il sera approvisionné avec succès. Si vous tentez d’approvisionner un utilisateur avec le département « Ventes » et l’emplacement « LA », l’utilisateur ne sera pas approvisionné. L’emplacement LA doit être ajouté à la table de référence dans ServiceNow ou l’attribut utilisateur dans Azure AD doit être mis à jour pour correspondre au format dans ServiceNow. 
* **EntryJoiningPropertyValueIsMissing :** Passez en revue vos [mappages d’attributs](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) pour identifier l’attribut correspondant. Cette valeur doit être présente sur l’utilisateur ou le groupe que vous tentez d’approvisionner. 
* Passez en revue l’[API SOAP de ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) pour comprendre les exigences ou les limitations (par exemple, le format pour spécifier l’indicatif du pays pour un utilisateur).
* Certains déploiements ServiceNow nécessitent l’autorisation des plages d’adresses IP pour le service d’approvisionnement Azure AD. Les plages d’adresses IP réservées pour le service d’approvisionnement Azure AD se trouvent [ici](https://www.microsoft.com/download/details.aspx?id=56519) sous « AzureActiveDirectoryDomainServices ».
* L’approvisionnement des utilisateurs vers le cloud du secteur public ServiceNow n’est pas pris en charge actuellement.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
