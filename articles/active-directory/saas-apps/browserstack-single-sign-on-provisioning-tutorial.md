---
title: 'Tutoriel : Configurer BrowserStack Single Sign-On pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment provisionner et déprovisionner automatiquement des comptes d’utilisateur entre Azure AD et BrowserStack Single Sign-On.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 39999abc-e4a2-4058-81e0-bf88182f8864
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2021
ms.author: Zhchia
ms.openlocfilehash: 200338ac50bceae6c3bf9730ee92a16c04a84b69
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108141448"
---
# <a name="tutorial-configure-browserstack-single-sign-on-for-automatic-user-provisioning"></a>Tutoriel : Configurer BrowserStack Single Sign-On pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à effectuer dans BrowserStack Single Sign-On et Azure AD (Active Directory) pour configurer le provisionnement automatique d’utilisateurs. Une fois configuré, Azure AD provisionne et déprovisionne automatiquement les utilisateurs pour [BrowserStack Single Sign-On](https://www.browserstack.com) à l’aide du service de provisionnement Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans BrowserStack Single Sign-On
> * Supprimez des utilisateurs dans BrowserStack Single Sign-On quand ils ne nécessitent plus d’accès
> * Maintien de la synchronisation des attributs utilisateur entre Azure AD et BrowserStack Single Sign-On
> * [Authentification unique](./browserstack-single-sign-on-tutorial.md) sur BrowserStack Single Sign-On (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](../roles/permissions-reference.md) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général). 
* Un compte d’utilisateur dans BrowserStack avec des autorisations de **propriétaire**.
* Un [Plan Entreprise](https://www.browserstack.com/pricing) avec BrowserStack. 
* Intégration de [Single Sign-On](https://www.browserstack.com/docs/enterprise/single-sign-on/azure-ad) avec BrowserStack (obligatoire).

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et BrowserStack Single Sign-On](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-browserstack-single-sign-on-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer BrowserStack Single Sign-On pour prendre en charge le provisionnement avec Azure AD

1. Connectez-vous à [BrowserStack](https://www.browserstack.com/users/sign_in) en tant qu’utilisateur disposant des autorisations de **propriétaire**.

2. Accédez à **Compte** -> **Paramètres et autorisations**. Sélectionnez l'onglet **Sécurité** .

3. Sous **Approvisionnement automatique d’utilisateurs**, cliquez sur **Configurer**.

    ![Paramètres](media/browserstack-single-sign-on-provisioning-tutorial/configure.png)

4. Sélectionnez les attributs utilisateur que vous souhaitez contrôler via Azure AD, puis cliquez sur **Confirmer**.

    ![Utilisateur](media/browserstack-single-sign-on-provisioning-tutorial/attributes.png)

5. Copiez les valeurs **URL de locataire** et **Jeton secret**. Vous entrerez ces valeurs dans les champs URL de locataire et Jeton secret de l’onglet Provisionnement de votre application BrowserStack Single Sign-On dans le portail Azure. Cliquez sur **Done**.

    ![Autorisation](media/browserstack-single-sign-on-provisioning-tutorial/credential.png)

6. Votre configuration d’approvisionnement a été enregistrée sur BrowserStack. **Activez** l’approvisionnement d’utilisateurs dans BrowserStack une fois **la configuration de l’approvisionnement terminée sur Azure AD**, pour empêcher le blocage à l’invitation de nouveaux utilisateurs à partir d’un [compte](https://www.browserstack.com/accounts/manage-users) BrowserStack. 

    ![Compte](media/browserstack-single-sign-on-provisioning-tutorial/enable.png)
    
## <a name="step-3-add-browserstack-single-sign-on-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter BrowserStack Single Sign-On à partir de la galerie d’applications Azure AD

Ajoutez BrowserStack Single Sign-On à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement avec BrowserStack Single Sign-On. Si vous avez déjà configuré BrowserStack Single Sign-On pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service de provisionnement Azure AD vous permet de définir l’étendue des utilisateurs provisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur. Si vous choisissez de définir l’étendue du provisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs à l’application. Si vous choisissez de définir l’étendue du provisionnement en fonction uniquement des attributs de l’utilisateur, vous pouvez utiliser un filtre d’étendue comme décrit [ici](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quand vous attribuez des utilisateurs à BrowserStack Single Sign-On, vous devez sélectionner un rôle autre que **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs à l’application. Lorsque l’étendue est définie sur tous les utilisateurs, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-browserstack-single-sign-on"></a>Étape 5. Configurer le provisionnement automatique d’utilisateurs sur BrowserStack Single Sign-On 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs dans l’application en fonction des affectations d’utilisateurs dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-browserstack-single-sign-on-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour BrowserStack Single Sign-On dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **BrowserStack Single Sign-on**.

    ![Lien BrowserStack Single Sign-On dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, renseignez les champs URL de locataire et Jeton secret pour BrowserStack Single Sign-On. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à BrowserStack Single Sign-On. Si la connexion échoue, vérifiez que votre compte BrowserStack Single Sign-On dispose d’autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Sous la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec BrowserStack Single Sign-On**.

9. Dans la section **Mappage d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et BrowserStack Single Sign-On. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour établir une correspondance avec les comptes d’utilisateur dans BrowserStack Single Sign-On pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vous devez vérifier que l’API BrowserStack Single Sign-On prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|--|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_role|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_team|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_product|String|


10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service de provisionnement Azure AD pour BrowserStack Single Sign-On, attribuez la valeur **Activé** au paramètre **État de l’approvisionnement** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous voulez provisionner sur BrowserStack Single Sign-On en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs définis sous **Étendue**, dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

- Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
- Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
- Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitations du connecteur

* BrowserStack Single Sign-On ne prend pas en charge l’approvisionnement de groupes.
* BrowserStack Single Sign-On nécessite que **emails[type eq "work"].value** et **userName** aient la même valeur source.

## <a name="troubleshooting-tips"></a>Conseils de dépannage

* Reportez-vous au guide de résolution des problèmes [ici](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#troubleshooting).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configuration des mappages d’attributs dans BrowserStack Single Sign-On](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad)
* [Configurer et activer l’approvisionnement automatique d’utilisateurs dans BrowserStack](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#setup-and-enable-auto-user-provisioning)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)