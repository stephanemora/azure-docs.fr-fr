---
title: "Tutoriel : Configurer UNIFI pour l'approvisionnement automatique d'utilisateurs avec Azure Active Directory | Microsoft Docs"
description: Apprenez à procéder automatiquement à l'approvisionnement et au déprovisionnement de comptes d'utilisateur d'Azure AD vers UNIFI.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 924c603f-574e-4e0a-9345-0cb0c7593dbb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2021
ms.author: Zhchia
ms.openlocfilehash: 7e9e9667c18375f9ec459f7e8c038d68f9b54299
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108186648"
---
# <a name="tutorial-configure-unifi-for-automatic-user-provisioning"></a>Tutoriel : Configurer UNIFI pour l'approvisionnement automatique d'utilisateurs

Ce tutoriel décrit les étapes à suivre dans UNIFI et Azure Active Directory (Azure AD) pour configurer l'approvisionnement automatique d'utilisateurs. Une fois la configuration effectuée, Azure AD approvisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [UNIFI](http://www.unifilabs.com/) à l'aide du service d'appprovisionnement d'Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans UNIFI
> * Supprimer des utilisateurs d'UNIFI lorsqu'ils n'ont plus besoin d'y accéder
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et UNIFI
> * Approvisionner des groupes et des appartenances à des groupes dans UNIFI
> * [Authentification unique](unifi-tutorial.md) auprès d'UNIFI (recommandé).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un locataire [UNIFI](http://www.unifilabs.com/).
* Un compte d'utilisateur dans UNIFI avec autorisations d'administration.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et UNIFI](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-unifi-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer UNIFI pour prendre en charge l'approvisionnement avec Azure AD

1. Assurez-vous que l'authentification unique est bien activée pour votre application d'entreprise dans Azure. 
1. Recherchez l'**URL de connexion** dans Authentification unique. Dans notre cas, il s'agit de `https://login.microsoftonline.com/<guid>/saml2`.
1. Dans la section Certificat de signature SAML, téléchargez le Certificat (Base64).

    ![Vue Authentification unique de l'application d'entreprise](media/unifi-provisioning-tutorial/enterprise-application-view.png)

1. Si votre fournisseur d'identité n'a pas encore été ajouté à UNIFI, connectez-vous au portail UNIFI en tant qu'**Administrateur d'entreprise**. Accédez à **Utilisateurs -> Configurer l'authentification unique -> bouton Ajouter un fournisseur**.

    ![Vue Ajouter un fournisseur d'identité](media/unifi-provisioning-tutorial/add-identity-provider-view.png)

1. Le modal permettant d'ajouter un fournisseur d'authentification unique s'affiche.

    ![Modal Ajouter un fournisseur d'identité](media/unifi-provisioning-tutorial/add-identity-provider-modal.png)

1. Entrez le **Nom** unique de votre choix. L'**URL** sera l'**URL de connexion** de votre application d'entreprise Azure AD. Entrez une valeur pour le **Jeton**. Entrez la valeur de votre Certificat (Base64) dans le champ **Certificat**. Si vous souhaitez que tous les utilisateurs créés ultérieurement utilisent ce fournisseur d'identité, cochez la case **Utiliser ce fournisseur d'identité par défaut**.

    ![Modal Ajouter un fournisseur d'identité renseigné](media/unifi-provisioning-tutorial/add-identity-provider-modal-populated.png)

1. Cliquez sur le bouton ENREGISTRER.

## <a name="step-3-add-unifi-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter UNIFI à partir de la galerie d'applications Azure AD

Ajoutez UNIFI à partir de la galerie d'applications Azure AD afin de commencer à gérer l'approvisionnement pour UNIFI. Si vous avez déjà configuré UNIFI pour l'authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lorsque vous attribuez des utilisateurs et des groupes à UNIFI, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-unifi"></a>Étape 5. Configurer l'approvisionnement automatique d'utilisateurs pour UNIFI 

Cette section vous guide tout au long des étapes de configuration du service d'approvisionnement d'Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans UNIFI en fonction des attributions d'utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-unifi-in-azure-ad"></a>Pour configurer l'approvisionnement automatique d'utilisateurs pour UNIFI dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **UNIFI**.

    ![Lien UNIFI dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Sous la section **Informations d'identification de l'administrateur**, saisissez l'**URL du locataire** -`https://licensing.inviewlabs.com/api/scim/v2/` UNIFI et le **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu'Azure AD peut se connecter à UNIFI. Si la connexion échoue, vérifiez que votre compte UNIFI dispose des autorisations d'administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec UNIFI**.

1. Dans la section **Mappages d'attributs**, passez en revue les attributs des utilisateurs qui sont synchronisés entre Azure AD et UNIFI. Les attributs sélectionnés en tant que propriétés de **Correspondance** utilisés dans le but de faire correspondre les comptes d'utilisateur dans UNIFI pour les opérations de mise à jour. Si vous choisissez de changer l'[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l'API UNIFI prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|


1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec UNIFI**.

1. Dans la section **Mappage d'attributs**, passez en revue les attributs des groupes qui sont synchronisés entre Azure AD et UNIFI. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés dans le but de faire correspondre les groupes dans UNIFI pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |membres|Informations de référence|
      |externalId|String|      

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service d'approvisionnement d'Azure AD pour UNIFI, définissez le paramètre **État d'approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou les groupes que vous souhaitez attribuer à UNIFI en choisissant les valeurs souhaitées sous **Étendue**, dans la section **Paramètres**.

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