---
title: 'Didacticiel : Approvisionnement d’utilisateurs pour Slack – Azure AD'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Slack.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd8e483d6c189e311fdb1925ad0f2effc2affe1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849142"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutoriel : Configurer Slack pour l’attribution automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans Slack et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur d’Azure AD vers Slack.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un client Azure Active Directory
* Un client Slack avec le [forfait Plus](https://aadsyncfabric.slack.com/pricing) ou mieux activé
* Un compte d’utilisateur dans Slack avec les autorisations d’administrateur d’équipe

Remarque : L’intégration du provisionnement Azure AD s’appuie sur [l’API Slack SCIM](https://api.slack.com/scim) qui est disponible pour les équipes Slack disposant du forfait Plus ou mieux.

## <a name="assigning-users-to-slack"></a>Affectation d’utilisateurs à Slack

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique en comptes utilisateur, les utilisateurs et les groupes qui ont été « attribués » à une application dans Azure AD seront synchronisés.

Avant de configurer et activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application Slack. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs à votre application Slack en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Conseils importants pour l’affectation d’utilisateurs à Slack

* Il est recommandé qu’un seul utilisateur Azure AD soit affecté à Slack pour tester la configuration du provisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Lorsque vous affectez un utilisateur à Slack, vous devez sélectionner le rôle **Utilisateur** ou « Groupe » dans la boîte de dialogue d’attribution. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="configuring-user-provisioning-to-slack"></a>Configuration de l'approvisionnement des utilisateurs sur Slack 

Cette section vous guide à travers la connexion de votre instance d’Azure AD au compte d’utilisateur fournissant l’API et la configuration du service de provisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Slack en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

**Conseil :** Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Slack en suivant les instructions disponibles dans le [Portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Pour configurer l’approvisionnement automatique de comptes utilisateur vers Slack dans Azure AD :

1. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré Slack pour l’authentification unique, recherchez votre instance de Slack à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Slack** dans la galerie d’applications. Sélectionnez Slack dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de Slack, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

   ![Approvisionnement Slack](./media/slack-provisioning-tutorial/slack1.png)

5. Sous la section **informations d’identification de l’administrateur**, cliquez sur **Autoriser**. Une boîte de dialogue d’autorisation Slack s’ouvre dans une nouvelle fenêtre de navigateur.

6. Dans la nouvelle fenêtre, connectez-vous à Slack à l’aide de votre compte d’administrateur d’équipe. Dans la boîte de dialogue d’autorisation qui s’affiche, sélectionnez l’équipe Slack pour laquelle vous souhaitez activer l’approvisionnement, puis sélectionnez **Autoriser**. Une fois cela terminé, revenez au portail Azure pour terminer la configuration de l’approvisionnement.

    ![Boîte de dialogue d’autorisation](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application Slack. Si la connexion échoue, vérifiez que votre compte Slack dispose des autorisations d’administrateur d’équipe et recommencez l’étape « Autoriser ».

8. Entrez l’adresse de messagerie d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **Adresse électronique de notification**, puis cochez la case se trouvant en dessous.

9. Cliquez sur **Enregistrer**.

10. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Slack**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui seront synchronisés d’Azure AD vers Slack. Notez que les attributs sélectionnés en tant que propriétés de **Correspondance** seront utilisés pour faire correspondre les comptes d’utilisateur dans Slack pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

12. Pour activer le service d’approvisionnement Azure AD pour Slack, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**

13. Cliquez sur **Enregistrer**.

Cette commande démarre la synchronisation initiale des utilisateurs et/ou groupes affectés à Slack dans la section Utilisateurs et Groupes. Notez que la synchronisation initiale prendra plus de temps que les synchronisations suivantes, qui se produisent toutes les 10 minutes environ, tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service de configuration dans votre application Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Facultatif] Configuration de l’approvisionnement d’objets de groupe vers Slack

Si vous le souhaitez, vous pouvez activer l’approvisionnement des objets de groupe d’Azure AD vers Slack. Cela est différent de « l’affectation de groupes d’utilisateurs », dans la mesure où l’objet de groupe réel sera répliqué en plus de ses membres d’Azure AD vers Slack. Par exemple, si vous avez un groupe nommé « Mon groupe » dans Azure AD, un groupe identique nommé « Mon groupe » est créé dans Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Pour activer l’approvisionnement des objets de groupe :

1. Dans la section Mappages, sélectionnez **Synchroniser les groupes Azure Active Directory avec Slack**.

2. Dans le volet de Mappage des attributs, définissez Activé sur Oui.

3. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui seront synchronisés d’Azure AD vers Slack. Notez que les attributs sélectionnés en tant que propriétés de **Correspondance** seront utilisés pour faire correspondre les groupes dans Slack pour les opérations de mise à jour. 

4. Cliquez sur **Enregistrer**.

Ainsi, les objets de groupe affectés à Slack dans la section **Utilisateurs et groupes** seront entièrement synchronisés d’Azure AD vers Slack. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application Slack.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Quand vous configurez l’attribut **displayName** de Slack, tenez compte des comportements suivants :

  * Les valeurs ne sont pas entièrement uniques (par exemple, 2 utilisateurs peuvent avoir le même nom complet).

  * Les caractères non anglais, les espaces et les majuscules sont pris en charge. 
  
  * Les signes de ponctuation autorisés sont notamment les points, traits de soulignement, traits d’union, apostrophes, crochets (par exemple, **( [ { } ] )** ) et séparateurs (par exemple, **, / ;** ).
  
  * La mise à jour n’est effectuée que si ces deux paramètres sont configurés dans l’espace de travail/organisation de Slack : **Profile syncing is enabled** (La synchronisation des profils est activée) et **Users cannot change their display name** (Les utilisateurs ne peuvent pas changer leur nom d’affichage).
  
* L’attribut **userName** ne doit pas dépasser 21 caractères et sa valeur doit être unique.

* Slack n’autorise que la correspondance avec les attributs **userName** et **email**.  

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
