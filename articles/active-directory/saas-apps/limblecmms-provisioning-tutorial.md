---
title: 'Tutoriel : Configurer LimbleCMMS pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et LimbleCMMS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5e0d5369-7230-4a16-bc3f-9eac2bc80a8c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2021
ms.author: Zhchia
ms.openlocfilehash: 6866b907f98776d7825567cec36687c44d77e404
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111852077"
---
# <a name="tutorial-configure-limblecmms-for-automatic-user-provisioning"></a>Tutoriel : Configurer LimbleCMMS pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans LimbleCMMS et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois la configuration terminée, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [LimbleCMMS](https://limblecmms.com/) par le biais du service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans LimbleCMMS
> * Supprimer des utilisateurs dans LimbleCMMS quand ils n’ont plus besoin d’y accéder
> * Créer des groupes dans LimbleCMMS
> * Ajouter/supprimer des utilisateurs dans les groupes dans LimbleCMMS
> * Supprimer des groupes dans LimbleCMMS
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et LimbleCMMS
> * Provisionner des groupes et des appartenances aux groupes dans LimbleCMMS
> * Utiliser l’[authentification unique](../manage-apps/add-application-portal-setup-oidc-sso.md) pour LimbleCMMS (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md). 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un locataire [LimbleCMMS](https://limblecmms.com/signup/?plan=business-yearly) avec une licence Business Plus ou d’un niveau supérieur.
* Un compte d’utilisateur LimbleCMMS doté d’autorisations de super administrateur.
* L’authentification unique activée dans votre locataire LimbleCMMS (contactez votre responsable Réussite clients).
* Au moins un groupe que vous prévoyez de provisionner dans LimbleCMMS (les autorisations dans LimbleCMMS sont basées sur les groupes ; si vous ne provisionnez aucun groupe, les utilisateurs qui seront provisionnés n’auront donc pas d’autorisations associées). 


## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
1. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Déterminez les données à [mapper entre Azure AD et LimbleCMMS](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-limblecmms-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer LimbleCMMS pour la prise en charge du provisionnement avec Azure AD

1. Connectez-vous à LimbleCMMS en tant que **Super administrateur**.
1. Accédez à **Paramètres avancés > Gérer l’authentification unique**.
      ![Gérer l’authentification unique](media/limblecmms-provisioning-tutorial/limble-manage-sso.png)
1. Sélectionnez **Azure Active Directory** comme fournisseur d’authentification unique.
1. [Configurez OIDC](https://help.limblecmms.com/en/articles/4446986-active-directory-oidc-sso-setup-guide) pour la prise en charge de l’authentification unique.
1. Cliquez sur le bouton **Générer un jeton SCIM** pour récupérer votre jeton SCIM, puis enregistrez ce jeton en vue d’une étape ultérieure.
1. Cliquez sur **Activer l’authentification unique**.

## <a name="step-3-add-limblecmms-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter LimbleCMMS à partir de la galerie d’applications Azure AD

Ajoutez LimbleCMMS à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans LimbleCMMS. Si vous avez déjà configuré LimbleCMMS pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez (provisionnez) des utilisateurs et des groupes à LimbleCMMS, vous devez sélectionner un autre rôle que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-limblecmms"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs dans LimbleCMMS 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans LimbleCMMS sur la base des attributions d’utilisateurs et/ou de groupes définies dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-limblecmms-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs dans LimbleCMMS à partir d’Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **LimbleCMMS**.

    ![Lien LimbleCMMS dans la liste des applications](common/all-applications.png)

1. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

1. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

1. Dans la section **Informations d’identification de l’administrateur**, entrez l’**URL de locataire** et le **Jeton secret** pour LimbleCMMS. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à LimbleCMMS.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec LimbleCMMS**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et LimbleCMMS. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans LimbleCMMS dans le cadre des opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vérifiez que l’API SCIM LimbleCMMS prend en charge le filtrage des utilisateurs selon cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|


1. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec LimbleCMMS**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et LimbleCMMS. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des groupes dans LimbleCMMS dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;
      |membres|Informations de référence|
      |externalId|String|      

1. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pour activer le service de provisionnement Azure AD pour LimbleCMMS, définissez **État de provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

1. Définissez les utilisateurs et/ou les groupes que vous souhaitez provisionner dans LimbleCMMS en sélectionnant les valeurs appropriées dans le champ **Étendue** de la section **Paramètres**.

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