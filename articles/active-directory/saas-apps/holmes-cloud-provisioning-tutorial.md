---
title: 'Tutoriel : Configurer Holmes Cloud pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Holmes Cloud.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: b1088904-2ea2-4440-b39e-c4b7712b8229
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: thwimmer
ms.openlocfilehash: 82fdfb7621762828a32ad67774e9b0141c6b0464
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207304"
---
# <a name="tutorial-configure-holmes-cloud-for-automatic-user-provisioning"></a>Tutoriel : Configurer Holmes Cloud pour l’approvisionnement automatique d’utilisateurs

Ce tutoriel décrit la procédure à suivre dans Holmes Cloud et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et déprovisionne automatiquement des utilisateurs et des groupes vers [Holmes Cloud](https://www.holmescloud.com/) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créez des utilisateurs dans Holmes Cloud.
> * Supprimez des utilisateurs dans Holmes Cloud lorsqu’ils n’ont plus besoin d’accès.
> * Conservez les attributs utilisateur synchronisés entre Azure AD et Holmes Cloud.
> * Approvisionnez des groupes et des appartenances aux groupes dans Holmes Cloud.
> * [Authentification unique](holmes-tutorial.md) sur Holmes Cloud (recommandé).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md). 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un locataire [Cloud Holmes](https://www.holmescloud.com/).
* Un compte d’utilisateur dans Holmes Cloud avec des autorisations d’administrateur.
* Un abonnement Holmes Cloud dans lequel l’authentification unique et le service d’approvisionnement d’utilisateurs sont activés.


## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et Holmes Cloud](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-holmes-cloud-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Holmes Cloud pour prendre en charge l’approvisionnement avec Azure AD

> [!NOTE]
> * Vous allez recevoir l’URL du locataire Holmes Cloud de l’équipe **Support Cloud Holmes** <cs@holmescloud.com> après avoir acheté l’abonnement.
> * Vous trouverez les informations requises (URL de point de terminaison, jeton, etc.) pour configurer le service d’approvisionnement sur la page **Paramètres de la société**, à condition de vous abonner à l’authentification unique et au service d’approvisionnement d’utilisateurs.
 
1. Connectez-vous au compte Holmes Cloud avec vos informations d’identification Holmes Cloud.
1. Sélectionnez le menu « 会社設定 (Paramètres de la société) », puis sélectionnez l’icône en forme de chapeau.
1. Consultez les informations, telles que le jeton d’API, dans le menu carte intitulé « アカウントプロビジョニング (Approvisionnement de comptes) ».
1. Pour la régénération des jetons, sélectionnez le lien « APIキーを発行する (Émettre une clé API) ».


## <a name="step-3-add-holmes-cloud-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Holmes Cloud à partir de la galerie d’applications Azure AD

Ajoutez Holmes Cloud à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement dans Holmes Cloud. Si vous avez déjà configuré Holmes Cloud pour l’authentification unique (SSO), vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Lors de l’attribution d’utilisateurs et de groupes dans Holmes Cloud, vous devez choisir un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-holmes-cloud"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs à Holmes Cloud 

Cette section vous guide dans la procédure de configuration du service d’approvisionnement Azure AD afin de créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Holmes Cloud en fonction des affectations des utilisateurs et/ou des groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-holmes-cloud-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Holmes Cloud dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **Holmes Cloud**.

    ![Lien Holmes Cloud dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification de l’administrateur**, entrez l’**URL du locataire** et le **Jeton secret**. Cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Holmes Cloud. En cas d’échec de la connexion, vérifiez que votre compte Holmes Cloud dispose d’autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser des utilisateurs Azure Active Directory avec Holmes Cloud**.

1. Passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Holmes Cloud dans la section **Mappages d’attributs**. Les attributs sélectionnés en tant que propriétés **correspondantes** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans Holmes Cloud dans le cadre d’opérations de mise à jour. Si vous décidez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API Holmes Cloud prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |displayName|String|
   |externalId|String|


1. Sous la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Holmes Cloud**.

1. Passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Holmes Cloud dans la section **Mappages d’attributs**. Les attributs sélectionnés en tant que propriétés **correspondantes** sont utilisés pour la mise en correspondance des groupes dans Holmes Cloud dans le cadre d’opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |membres|Informations de référence|
      |externalId|String|      

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service d’approvisionnement Azure AD pour Holmes Cloud, définissez l’**État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner dans Holmes Cloud en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

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