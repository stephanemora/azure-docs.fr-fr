---
title: 'Tutoriel : Configurer Sigma Computing pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et Sigma Computing.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6108a4de-4420-4baa-bc2f-1c39a1ebe81d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2021
ms.author: Zhchia
ms.openlocfilehash: 71fb104594de9f3017b07ad455b4a8ccd7f57073
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758098"
---
# <a name="tutorial-configure-sigma-computing-for-automatic-user-provisioning"></a>Tutoriel : Configurer Sigma Computing pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à effectuer dans Sigma Computing et Azure AD (Azure Active Directory) pour configurer le provisionnement automatique d’utilisateurs. Une fois la configuration terminée, Azure AD provisionne et déprovisionne automatiquement les utilisateurs et les groupes dans [Sigma Computing](https://www.sigmacomputing.com/) par le biais du service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Sigma Computing
> * Supprimer des utilisateurs dans Sigma Computing quand ils n’ont plus besoin d’y accéder
> * Maintenir la synchronisation des attributs utilisateur entre Azure AD et Sigma Computing
> * Provisionner des groupes et des appartenances aux groupes dans Sigma Computing
> * Utiliser l’[authentification unique](https://docs.microsoft.com/azure/active-directory/saas-apps/sigma-computing-tutorial) pour Sigma Computing (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un compte d’administrateur dans votre organisation Sigma.
* Intégration de l’[authentification unique](https://docs.microsoft.com/azure/active-directory/saas-apps/sigma-computing-tutorial) existante avec Sigma Computing.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et Sigma Computing](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-sigma-computing-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Sigma Computing pour la prise en charge du provisionnement avec Azure AD

1. Connectez-vous à votre compte Sigma.

2. Accédez au **portail d’administration** en sélectionnant **Administration** dans le menu utilisateur.

3. Dans le volet gauche, cliquez sur **Authentification** pour ouvrir la page d’authentification de votre organisation.

4. Vérifiez que la **méthode d’authentification** est définie sur **SAML** uniquement.

5. Cliquez sur le bouton **Configurer** sous **Type de compte et provisionnement d’équipe** pour ouvrir le modal Provisionnement.

   ![Rôle](media/sigma-computing-provisioning-tutorial/sigma-role-and-team-provisioning.png)

6. Lisez les notes fournies dans la section Bien démarrer du modal Provisionnement. Cochez la case de confirmation, puis cliquez sur **Suivant** pour continuer.

7. Entrez un nom de jeton, puis cliquez sur **Suivant**.

   ![Suivant](media/sigma-computing-provisioning-tutorial/sigma-create-token.png)

8. Sigma vous fournira un **jeton de porteur** et l’**URL de base du répertoire**. Copiez et enregistrez ces valeurs en lieu sûr. Ces valeurs devront être entrées dans les champs **URL de locataire** et **Jeton secret** sous l’onglet Provisionnement de votre application Sigma Computing sur le portail Azure. Cliquez sur **Done**.

   ![Sigma](media/sigma-computing-provisioning-tutorial/sigma-copy-keys.png)
   
## <a name="step-3-add-sigma-computing-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Sigma Computing à partir de la galerie d’applications Azure AD

Ajoutez Sigma Computing à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans Sigma Computing. Si vous avez déjà configuré Sigma Computing pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quand vous attribuez (provisionnez) des utilisateurs et des groupes à Sigma Computing, vous devez sélectionner un autre rôle que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-sigma-computing"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs dans Sigma Computing 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-sigma-computing-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs dans Sigma Computing à partir d’Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

   ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Sigma Computing**.

   ![Lien Sigma Computing dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

   ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

   ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez l’URL du locataire et le jeton secret pour Sigma Computing. Cliquez sur **Tester la connexion** pour vérifier qu'Azure AD peut se connecter à Sigma Computing. Si la connexion échoue, vérifiez que votre compte Sigma Computing dispose des autorisations d’administrateur, puis réessayez.

   ![Auth](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

   ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Sigma Computing**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Sigma Computing. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans Sigma Computing dans le cadre des opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vous devez vérifier que l’API Sigma Computing prend en charge le filtrage des utilisateurs selon cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |userType|String|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|


10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Sigma Computing**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Sigma Computing. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des groupes dans Sigma Computing dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;|
      |membres|Informations de référence|

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour Sigma Computing, définissez **État de provisionnement** sur **Activé** dans la section **Paramètres**.

   ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou les groupes que vous souhaitez provisionner dans Sigma Computing en sélectionnant les valeurs appropriées dans le champ **Étendue** dans la section **Paramètres**.

   ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

   ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
