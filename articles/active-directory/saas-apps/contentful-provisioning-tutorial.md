---
title: 'Tutoriel : Configuration de Contentful pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner automatiquement des comptes d’utilisateur d’Azure AD pour Contentful et comment annuler ce provisionnement.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: Zhchia
ms.openlocfilehash: 4ff08e51f6e3b2ae72da43052c25046be8bb5397
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352148"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Tutoriel : Configuration de Contentful pour l’attribution automatique d’utilisateurs

Ce tutoriel décrit la procédure à suivre dans Contentful et Azure Active Directory (Azure AD) pour configurer l’attribution automatique d’utilisateurs. Une fois configuré, Azure AD procède automatiquement à l’attribution et à l’annulation de l’attribution des utilisateurs et des groupes sur [Contentful](https://www.contentful.com/) à l’aide du service de provisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Contentful
> * Supprimer des utilisateurs dans Contentful lorsqu’ils n’ont plus besoin d’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Contentful
> * Provisionner des groupes et des appartenances aux groupes dans Contentful
> * [Authentification unique](./contentful-tutorial.md) auprès de Contentful (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un compte d’organisation Contentful avec un abonnement prenant en charge le provisionnement SCIM. Si vous avez des questions concernant l’abonnement de votre organisation, contactez Contentful à l’adresse [support@contentful.com](mailto:support@contentful.com).
 
## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Contentful](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-contentful-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Contentful pour prendre en charge le provisionnement avec Azure AD

1. Créez un compte **Utilisateur de service** dans Contentful. Toutes les autorisations de provisionnement pour Azure seront accordées par l’intermédiaire de ce compte. Il est recommandé de choisir le rôle d’organisation **Propriétaire** pour ce compte.

2. Connectez-vous à Contentful avec le compte **Utilisateur de service** créé à l’étape précédente.

3. Accédez au **Curseur de gauche** -> **Paramètres de l’organisation** -> **Outils d’accès** -> **Attribution d’utilisateurs**.

    ![Menu](media/contentful-provisioning-tutorial/access.png)

4. Copiez et enregistrez **l’URL SCIM**. Cette valeur sera entrée dans l’onglet Provisionnement de votre application Contentful sur le Portail Azure.

5. Cliquez sur **Générer un jeton d’accès personnel**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Dans la fenêtre modale, attribuez un nom explicite à votre jeton d’accès personnel, puis cliquez sur « Générer ».
    
7. **L’URL SCIM** et le **Jeton secret** sont générés. Copiez et enregistrez ces valeurs. Elles seront entrées dans l’onglet Provisionnement de votre application Contentful sur le Portail Azure.

    ![access](media/contentful-provisioning-tutorial/token.png)


Si vous avez des questions lors de la configuration du provisionnement sur la console d’administration Contentful, contactez [support@contentful.com](mailto:support@contentful.com).


## <a name="step-3-add-contentful-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Contentful à partir de la galerie d’applications Azure AD

Ajoutez Contentful à partir de la galerie d’applications Azure AD afin de commencer à gérer le provisionnement pour Contentful. Si vous avez déjà configuré Contentful pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous provisionnez des utilisateurs et des groupes pour Contentful, vous devez sélectionner un rôle autre que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-contentful"></a>Étape 5. Configurer l’attribution automatique d’utilisateurs pour Contentful 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour Contentful dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Contentful**.

    ![Lien Contentful dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification d’administration**, entrez l’URL et le jeton secret de votre locataire Contentful. Cliquez sur **Tester la connexion** pour voir si Azure AD peut se connecter à Contentful. Si la connexion échoue, vérifiez que votre compte Contentful dispose des autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Contentful**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur synchronisés entre Azure AD et Contentful. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Contentful dans le cadre des opérations de mise à jour. Si vous choisissez de modifier [l’attribut cible de correspondance](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Contentful prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Contentful**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Contentful. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Contentful dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;|
      |membres|Informations de référence|

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour Contentful, affectez la valeur **Activé** au paramètre **Statut de provisionnement** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et les groupes que vous souhaitez provisionner sur Contentful en choisissant les valeurs souhaitées dans le champ **Étendue** de la section **Paramètres**.

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