---
title: 'Tutoriel : Configurer SchoolStream ASA pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Apprenez à provisionner et déprovisionner automatiquement des comptes d’utilisateur depuis Azure AD pour SchoolStream ASA.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: ac594768-7b76-4e5a-b46e-8f1cb41f2754
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: thwimmer
ms.openlocfilehash: 5648c84fceb0c6d17375b712a4f3a86561296d4d
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709039"
---
# <a name="tutorial-configure-schoolstream-asa-for-automatic-user-provisioning-in-schoolstream-asa"></a>Tutoriel : Configurer SchoolStream ASA pour le provisionnement automatique d’utilisateurs dans SchoolStream ASA

Ce tutoriel décrit les étapes à suivre dans SchoolStream ASA et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois celui-ci configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [SchoolStream ASA](https://www.ssk12.com/) à l’aide du service Provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans SchoolStream ASA 
> * Supprimer des utilisateurs dans SchoolStream ASA quand ils n’ont plus besoin d’accès
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et SchoolStream ASA.
> * Provisionner des groupes et des appartenances aux groupes dans SchoolStream ASA.
> * [Authentification unique](../manage-apps/add-application-portal-setup-oidc-sso.md) dans SchoolStream ASA (recommandée)


## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un site web SchoolStream. Si vous n’en avez pas, contactez le [support SchoolStream](mailto:support@rtresponse.com).

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et SchoolStream ASA](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-schoolstream-asa-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer SchoolStream ASA pour prendre en charge le provisionnement avec Azure AD

1. Contactez le [support SchoolStream](mailto:support@rtresponse.com) pour demander l’intégration de SchoolStream ASA ; vous devrez fournir vos **Id de locataire Azure AD** et **URL de site web SchoolStream**.

1. Vous obtiendrez votre **Jeton secret** et l’**URL du locataire** SchoolStream ASA dès que SchoolStream aura mappé votre site web SchoolStream et l’ID de locataire Azure AD.

## <a name="step-3-add-schoolstream-asa-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter SchoolStream ASA à partir de la galerie d’applications Azure AD

Pour commencer la gestion du provisionnement de SchoolStream ASA depuis votre instance Azure AD, vous devez ajouter SchoolStream ASA à partir de la galerie d’applications Azure AD. 

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Parcourir la galerie Azure AD**, tapez **SchoolStream ASA** dans la zone de recherche.
1. Sélectionnez **SchoolStream ASA** dans le volet de résultats, puis **Inscrivez-vous à l’application**. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


Si vous avez déjà configuré SchoolStream ASA pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à SchoolStream ASA, vous devez sélectionner un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-schoolstream-asa"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs pour SchoolStream ASA 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans SchoolStream ASA, selon les affectations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-schoolstream-asa-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour SchoolStream ASA dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **SchoolStream ASA**.

    ![Le lien SchoolStream ASA dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1. Si vous configurez le provisionnement pour la première fois, sélectionnez **Démarrage**.

    ![Démarrage du provisionnement](media/schoolstream-asa-provisioning-tutorial/provisioning-get-started.png)
    
1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](media/schoolstream-asa-provisioning-tutorial/provisioning-automatic.png)

1. Sous la section **Informations d’identification de l’administrateur**, entrez l’**URL de locataire** et le **Jeton secret** de votre instance SchoolStream ASA. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à SchoolStream ASA. Si la connexion échoue, vérifiez que votre compte SchoolStream ASA dispose d’autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Sélectionnez **Enregistrer** pour pouvoir afficher la section **Paramètres**.

1. Dans le champ **E-mail de notification** de la section **Paramètres**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement, puis sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Dans la section **Mappages**, sélectionnez **Provisionner les utilisateurs Azure Active Directory**.

1. Sélectionnez **Ajouter un mappage** en bas.

1. Dans la boîte de dialogue **Modifier l’attribut** : 
    
   * Dans le champ **Type de mappage**, sélectionnez **Direct** dans la liste déroulante.
   * Dans le champ **Attribut de la source**, sélectionnez **extensionAttribute1** dans la liste déroulante.
   * Entrez votre **Id de locataire Azure AD** dans le champ **Valeur par défaut si la valeur est null (facultatif)** .
   * Dans le champ **Attribut cible**, sélectionnez **urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization** dans la liste déroulante. 
   * Dans le champ **Trouver les objets utilisant cet attribut**, sélectionnez **Non** dans la liste déroulante.
   * Dans le champ **Appliquer ce mappage**, sélectionnez **Toujours** dans la liste déroulante.
   * Sélectionnez **OK**.

       ![Modifier l’attribut](media/schoolstream-asa-provisioning-tutorial/add-mappings-attribute.png)

1. Dans la section **Mappage d’attributs**, passez en revue les attributs d’utilisateurs synchronisés entre Azure AD et SchoolStream ASA. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés au moment de faire correspondre les comptes d’utilisateur dans SchoolStream ASA pour les opérations de mise à jour. Si vous choisissez de changer l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API SchoolStream ASA prend en charge le filtrage des utilisateurs en fonction de cet attribut.



   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |displayName|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String| 


1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec UNIFI**.

1. Dans la section **Mappage d'attributs**, passez en revue les attributs des groupes qui sont synchronisés entre Azure AD et UNIFI. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés dans le but de faire correspondre les groupes dans UNIFI pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |membres|Informations de référence|
      |externalId|String|      


1. Cliquez sur le bouton **Enregistrer** pour valider les modifications. Vous pouvez revenir à l’onglet **Application** et sélectionner **Modifier le provisionnement** pour continuer.

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Vous activez le service de provisionnement Azure AD pour SchoolStream ASA en définissant le paramètre **État de provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou les groupes à provisionner pour SchoolStream ASA en choisissant les valeurs souhaitées dans le champ **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

1. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Journal des modifications

* 24/09/2020 - Le provisionnement de groupe a été activé.

## <a name="more-resources"></a>Plus de ressources

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)