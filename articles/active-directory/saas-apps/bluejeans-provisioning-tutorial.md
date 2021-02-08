---
title: 'Tutoriel : Configurer BlueJeans pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour attribuer et retirer automatiquement des utilisateurs dans BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d029f033a3c452587dbeeadf69c46cc99f604031
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053815"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Configurer BlueJeans pour l’attribution automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans BlueJeans et Azure Active Directory (Azure AD) pour configurer le provisionnement automatique d’utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs pour [BlueJeans](https://www.bluejeans.com/pricing) à l’aide du service de provisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans BlueJeans
> * Supprimer des utilisateurs dans BlueJeans quand ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et BlueJeans
> * [Authentification unique](https://docs.microsoft.com/azure/active-directory/saas-apps/bluejeans-tutorial) auprès de BlueJeans (recommandée)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md).
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un locataire BlueJeans pour lequel le plan [My Company](https://www.bluejeans.com/pricing) (Ma société) ou un plan supérieur est activé.
* Un compte d’utilisateur dans BlueJeans avec des autorisations d’administrateur.
* Provisionnement SCIM activé dans BlueJeans Enterprise.

> [!NOTE]
> L’intégration de l’attribution d’utilisateurs Azure AD repose sur [l’API BlueJeans](https://BlueJeans.github.io/developer), qui est mise à la disposition des équipes BlueJeans qui relèvent au moins du plan standard.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et BlueJeans](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer BlueJeans pour prendre en charge le provisionnement avec Azure AD

1. Connectez-vous à la console d’administration de BlueJeans. Accédez à Paramètres de groupe > Sécurité.
2. Sélectionnez **Authentification unique**, puis **Configurer**.

    ![now](./media/bluejeans-provisioning-tutorial/configure.png)

3. Dans la fenêtre **Provision & Integration** (Provisionnement et intégration), sélectionnez **Create and manage user accounts through IDP** (Créer et gérer des comptes d’utilisateur via IDP), puis cliquez sur **GÉNÉRER UN JETON**.

    ![générer](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Copiez et enregistrez le jeton. 
5. L’URL du locataire BlueJeans est `https://api.bluejeans.com/v2/scim`. L’**URL de locataire** et le **Jeton secret** de l’étape précédente sont entrés sous l’onglet Provisionnement de votre application BlueJeans dans le portail Azure.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter BlueJeans à partir de la galerie d’applications Azure AD

Ajoutez BlueJeans à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement auprès de BlueJeans. Si vous avez déjà configuré BlueJeans pour l’authentification SSO, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service de provisionnement Azure AD vous permet de définir l’étendue des utilisateurs provisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur. Si vous choisissez de définir l’étendue du provisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs à l’application. Si vous choisissez de définir l’étendue du provisionnement en fonction uniquement des attributs de l’utilisateur, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs à BlueJeans, vous devez sélectionner un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs à l’application. Lorsque l’étendue est définie sur tous les utilisateurs, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs auprès de BlueJeans

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs dans TestApp en fonction des affectations d’utilisateurs dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour BlueJeans dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **BlueJeans**.

    ![Lien BlueJeans dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’URL de locataire BlueJeans et le jeton secret récupérés à l’étape 2. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à BlueJeans. Si la connexion échoue, vérifiez que votre compte BlueJeans dispose des autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)


6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne qui doit recevoir les notifications d’erreur de provisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec BlueJeans**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et BlueJeans. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour établir une correspondance avec les comptes d’utilisateur BlueJeans en vue de mises à jour ultérieures. Si vous choisissez de changer l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API BlueJeans prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

|Attribut|Type|Pris en charge pour le filtrage|
|---|---|---|
|userName|String|&check;|
|active|Boolean|
|title|String|
|emails[type eq "work"].value|String|
|name.givenName|String|
|name.familyName|String|
|phoneNumbers[type eq "work"].value|String|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’attribution d’utilisateurs Azure AD pour BlueJeans, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs à provisionner auprès de BlueJeans en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs définis sous **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitations du connecteur

* Bluejeans n’autorise pas les noms d’utilisateurs (« userNames ») de plus de 30 caractères.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)