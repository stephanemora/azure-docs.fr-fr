---
title: 'Tutoriel : Configurer getAbstract pour le provisionnement automatique des utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateurs à partir d’Azure AD vers getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 25253e9a302a34fb473da63ad4cad562d6302a8a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651738"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Tutoriel : Configurer getAbstract pour le provisionnement automatique des utilisateurs

Ce tutoriel décrit les étapes à suivre dans getAbstract et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique des utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes sur [getAbstract](https://www.getabstract.com) à l’aide du service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans getAbstract.
> * Supprimer des utilisateurs dans getAbstract lorsqu’ils n’ont plus besoin d’accès.
> * Conserver les attributs utilisateur synchronisés entre Azure AD et getAbstract.
> * Provisionner des groupes et des appartenances aux groupes dans getAbstract.
> * [Authentification unique](getabstract-tutorial.md) auprès de getAbstract (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un locataire getAbstract (licence getAbstract Corporate).
* Authentification unique activée sur le locataire Azure AD et le locataire getAbstract.
* Activation de l’approbation et de SCIM pour getAbstract (envoyez un e-mail à b2b.itsupport@getabstract.com).

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et getAbstract](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer getAbstract pour prendre en charge le provisionnement avec Azure AD
1. Connexion à getAbstract
2. Cliquez sur l’icône des paramètres située dans le coin supérieur droit, puis cliquez sur l’option **My Central Admin** (Mon administrateur central).
 
    ![Mon administrateur central getAbstract](media/getabstract-provisioning-tutorial/my-account.png)

3. Recherchez et cliquez sur l’option **SCIM Admin** (Admin SCIM)
 
    ![Admin SCIM getAbstract](media/getabstract-provisioning-tutorial/scim-admin.png) 

4. Cliquez sur le bouton **Go** (Aller) 

    ![ID client SCIM getAbstract](media/getabstract-provisioning-tutorial/scim-client-go.png)

5. Cliquez sur le bouton **Generate new token** (Générer un nouveau jeton)

    ![Jeton 1 SCIM getAbstract](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

6. Si vous êtes sûr, cliquez sur le bouton **Generate new token** (Générer un nouveau jeton) Sinon, cliquez sur le bouton **Cancel** (Annuler)

    ![Jeton 2 SCIM getAbstract](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

7. Enfin, vous pouvez cliquer sur l’icône de copie dans le presse-papiers ou sélectionner l’intégralité du jeton et le copier. Notez également que l’URL du locataire/de base est `https://www.getabstract.com/api/scim/v2`. Ces valeurs seront entrées dans les champs **Secret Token*** (Jeton secret) et **Tenant URL*** (URL du locataire) de l’onglet Provisioning (Provisionnement) de votre application getAbstract dans le portail Azure.

    ![Jeton 3 SCIM getAbstract](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)


## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter getAbstract à partir de la galerie d’applications Azure AD

Ajoutez getAbstract à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans getAbstract. Si vous avez déjà configuré getAbstract pour l’authentification SSO, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs et des groupes à getAbstract, vous devez sélectionner un rôle autre que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Étape 5. Configurer le provisionnement automatique des utilisateurs dans getAbstract 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Pour configurer le provisionnement automatique des utilisateurs pour getAbstract dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **getAbstract**.

    ![Lien getAbstract dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Admin Credentials** (Informations d’identification de l’administrateur), entrez l’URL du locataire et le Jeton secret de getAbstract. Cliquez sur **Test Connection** (Tester la connexion) pour vérifier qu’Azure AD peut se connecter à getAbstract. Si la connexion échoue, vérifiez que votre compte getAbstract dispose des autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappings** (Mappages), sélectionnez **Synchronize Azure Active Directory Users to getAbstract** (Synchroniser les utilisateurs Azure Active Directory avec getAbstract).

9. Dans la section **Attribute-Mapping** (Mappages des attributs), vérifiez les attributs des utilisateurs qui sont synchronisés d’Azure AD à getAbstract. Les attributs sélectionnés en tant que propriétés **Matching** (Correspondance) sont utilisés pour faire correspondre les comptes d’utilisateurs dans getAbstract pour les opérations de mise à jour. Si vous choisissez de changer l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API getAbstract prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |preferredLanguage|String|

10. Sous la section **Mappings** (Mappages), sélectionnez **Synchronize Azure Active Directory Groups to getAbstract** (Synchroniser les groupes Azure Active Directory avec getAbstract).

11. Dans la section **Attribute-Mapping** (Mappages des attributs), vérifiez les attributs des groupes qui sont synchronisés d’Azure AD à getAbstract. Les attributs sélectionnés en tant que propriétés **Matching** (Correspondance) sont utilisés pour faire correspondre les groupes dans getAbstract pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    |Attribut|Type|Pris en charge pour le filtrage|
    |---|---|---|
    |displayName|String|&check;|
    |externalId|String|
    |membres|Informations de référence|
12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour getAbstract, définissez le paramètre **Provisioning Status** (État du provisionnement) sur **On** (Activé) dans la section **Settings** (Paramètres).

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou les groupes que vous souhaitez provisionner sur getAbstract en choisissant les valeurs souhaitées dans **Scope** (Étendue) de la section **Settings** (Paramètres).

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

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