---
title: 'Didacticiel : Configurer la réécriture SuccessFactors dans Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer la réécriture des attributs sur SuccessFactors à partir d’Azure AD.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: c780ee973c1dabb15c37b2519eb8253d2371080a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932130"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Didacticiel : Configurer la réécriture des attributs d’Azure AD sur SAP SuccessFactors (préversion)
L’objectif de ce tutoriel est de vous montrer les étapes à effectuer pour réécrire des attributs d’Azure AD sur SuccessFactors Employee Central. Le seul attribut actuellement pris en charge pour la réécriture est l’attribut email. 

## <a name="overview"></a>Vue d'ensemble

Après avoir configuré l’intégration du provisionnement entrant à l’aide de l’application de provisionnement [SuccessFactors vers AD local](sap-successfactors-inbound-provisioning-tutorial.md) ou [SuccessFactors vers Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md), vous pouvez éventuellement configurer l’application SuccessFactors Writeback pour réécrire l’adresse e-mail vers SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>À qui cette solution d’attribution d’utilisateurs convient-elle le mieux ?

Cette solution de provisionnement d’utilisateurs SuccessFactors Writeback est idéale pour :

* Les organisations qui utilisent Office 365 et qui souhaitent réécrire vers SuccessFactors des attributs faisant autorité gérés par le service informatique (par exemple, l’adresse e-mail).

## <a name="configuring-successfactors-for-the-integration"></a>Configuration de SuccessFactors pour l’intégration

L’une des exigences communes de tous les connecteurs de provisionnement SuccessFactors est qu’ils nécessitent les informations d’identification d’un compte SuccessFactors avec les autorisations appropriées pour appeler les API OData SuccessFactors. Cette section décrit les étapes permettant de créer le compte de service dans SuccessFactors et d’accorder les autorisations appropriées. 

* [Créer/identifier un compte d’utilisateur d’API dans SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Créer un rôle d’autorisations d’API](#create-an-api-permissions-role)
* [Créer un groupe d’autorisations pour l’utilisateur des API](#create-a-permission-group-for-the-api-user)
* [Accorder le rôle d’autorisation au groupe d’autorisations](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Créer/identifier un compte d’utilisateur d’API dans SuccessFactors
Collaborez avec votre équipe d’administration SuccessFactors ou votre partenaire d’implémentation pour créer ou identifier un compte d’utilisateur dans SuccessFactors qui sera utilisé pour appeler les API OData. Les informations d’identification de nom d’utilisateur et de mot de passe de ce compte seront requises lors de la configuration des applications de provisionnement dans Azure AD. 

### <a name="create-an-api-permissions-role"></a>Créer un rôle d’autorisations d’API

* Connectez-vous à SAP SuccessFactors avec un compte d’utilisateur qui a accès au centre d’administration.
* Recherchez *Manage Permission Roles* (Gérer les rôles d’autorisations), puis sélectionnez **Manage Permission Roles** dans les résultats de la recherche.
  ![Gérer les rôles d’autorisations](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Dans la liste Permission Role (Rôle d’autorisation), cliquez sur **Create New** (Créer).
  > [!div class="mx-imgBorder"]
  > ![Créer un rôle d’autorisation](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Ajoutez un **nom de rôle** et une **description** pour le nouveau rôle d’autorisation. Le nom et la description doivent indiquer que le rôle est destiné aux autorisations d’utilisation des API.
  > [!div class="mx-imgBorder"]
  > ![Détail du rôle d’autorisation](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Sous Permission settings (Paramètres d’autorisation), cliquez sur **Permission...** , faites défiler la liste des autorisations, puis cliquez sur **Manage Integration Tools** (Gérer les outils d’intégration). Cochez la case **Allow Admin to Access to OData API through Basic Authentication** (Autoriser l’administrateur à accéder à l’API OData via l’authentification de base).
  > [!div class="mx-imgBorder"]
  > ![Gérer les outils d’intégration](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Faites défiler la liste dans la même zone et sélectionnez **Employee Central API**. Ajoutez des autorisations comme indiqué ci-dessous pour lire à l’aide de l’API ODATA et modifier à l’aide de l’API ODATA. Sélectionnez l’option de modification si vous envisagez d’utiliser le même compte pour le scénario de réécriture vers SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Autorisations de lecture-écriture](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Cliquez sur **Done** (Terminé). Cliquez sur **Save Changes** (Enregistrer les modifications).

### <a name="create-a-permission-group-for-the-api-user"></a>Créer un groupe d’autorisations pour l’utilisateur des API

* Dans le centre d’administration SuccessFactors, recherchez *Manage Permission Groups* (Gérer les groupes d’autorisations), puis sélectionnez **Manage Permission Groups** dans les résultats de la recherche.
  > [!div class="mx-imgBorder"]
  > ![Gérer les groupes d’autorisations](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Dans la fenêtre Manage Permission Groups, cliquez sur **Create New**.
  > [!div class="mx-imgBorder"]
  > ![Ajouter un nouveau groupe](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Ajoutez un nom pour le nouveau groupe. Ce nom doit indiquer que le groupe est destiné aux utilisateurs des API.
  > [!div class="mx-imgBorder"]
  > ![Nom du groupe d’autorisations](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Ajoutez des membres au groupe. Par exemple, vous pouvez sélectionner **Username** dans le menu déroulant People Pool, puis entrer le nom d’utilisateur du compte d’API qui sera utilisé pour l’intégration. 
  > [!div class="mx-imgBorder"]
  > ![Ajouter des membres à un groupe](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Cliquez sur **Done** pour terminer la création du groupe d’autorisations.

### <a name="grant-permission-role-to-the-permission-group"></a>Accorder le rôle d’autorisation au groupe d’autorisations

* Dans le centre d’administration SuccessFactors, recherchez *Manage Permission Roles*, puis sélectionnez **Manage Permission Roles** dans les résultats de la recherche.
* Dans la liste **Permission Role List** (Liste des rôles d’autorisations), sélectionnez le rôle que vous avez créé pour les autorisations d’utilisation des API.
* Sous **Grant this role to...** (Attribuer ce rôle à), cliquez sur le bouton **Add...** (Ajouter).
* Sélectionnez **Permission Group...** (Groupe d’autorisations) dans le menu déroulant, puis cliquez sur **Select...** (Sélectionner) pour ouvrir la fenêtre Groups afin de rechercher et sélectionner le groupe créé ci-dessus. 
  > [!div class="mx-imgBorder"]
  > ![Ajouter un groupe d’autorisations](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Passez en revue l’octroi de rôle d’autorisation au groupe d’autorisations. 
  > [!div class="mx-imgBorder"]
  > ![Détails du rôle et du groupe d’autorisations](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Cliquez sur **Save Changes**.

## <a name="configuring-successfactors-writeback"></a>Configuration de SuccessFactors Writeback

Cette section décrit les étapes à suivre pour 

* [Ajouter l’application du connecteur de provisionnement et configurer la connectivité à SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors).
* [Configurer les mappages d'attributs](#part-2-configure-attribute-mappings).
* [Activer et lancer le provisionnement des utilisateurs](#enable-and-launch-user-provisioning).

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Partie 1 : Ajouter l’application du connecteur de provisionnement et configurer la connectivité à SuccessFactors

**Pour configurer SuccessFactors Writeback**

1. Accédez à <https://portal.azure.com>

2. Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3. Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4. Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5. Recherchez **SuccessFactors Writeback** et ajoutez cette application à partir de la galerie.

6. Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7. Définissez le **Mode** **Approvisionnement** sur **Automatique**

8. Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom de l’utilisateur administrateur** : entrez le nom d’utilisateur du compte d’utilisateur de l’API SuccessFactors, accompagné de l’ID d’entreprise. Son format est le suivant : **nom_utilisateur\@ID_société**

   * **Mot de passe d’administrateur** : entrez le mot de passe du compte d’utilisateur de l’API SuccessFactors. 

   * **URL du locataire** : entrez le nom du point de terminaison des services de l’API OData SuccessFactors. Entrez uniquement le nom d’hôte du serveur, sans http ou https. Cette valeur doit ressembler à ceci : **nom_serveur_API.successfactors.com**.

   * **E-mail de notification :** entrez votre adresse e-mail et cochez la case « Envoyer un e-mail en cas de défaillance ».
         > [!NOTE]
         > The Azure AD Provisioning Service sends email notification if the provisioning job goes into a [quarantine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) state.

   * Cliquez sur le bouton **Tester la connexion**. Si le test de connexion aboutit, cliquez sur le bouton **Enregistrer**, en haut de l'écran. En cas d’échec, vérifiez que les informations d’identification et l’URL SuccessFactors sont valides.
    >[!div class="mx-imgBorder"]
    >![Portail Azure](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Une fois les informations d’identification enregistrées, la section **Mappages** affiche le mappage par défaut **Synchroniser les utilisateurs d’Azure Active Directory avec SuccessFactors**.

### <a name="part-2-configure-attribute-mappings"></a>Partie 2 : Configuration des mappages d’attributs

Dans cette section, vous allez configurer le flux des données utilisateur de SuccessFactors vers Active Directory.

1. Sous l’onglet Provisionnement, sous **Mappages**, cliquez sur **Synchroniser les utilisateurs d’Azure Active Directory avec SuccessFactors**.

1. Dans le champ **Portée de l’objet source**, vous pouvez sélectionner les ensembles d’utilisateurs Azure AD qui doivent être pris en compte pour Writeback, en définissant des filtres basés sur des attributs. La portée par défaut est « tous les utilisateurs dans Azure AD ». 
   > [!TIP]
   > Lors de la configuration initiale de l'application d'approvisionnement, vous devez tester et vérifier vos mappages d'attributs et expressions pour être sûr d'obtenir le résultat souhaité. Microsoft vous recommande d’utiliser les filtres d’étendue disponibles sous **Portée de l’objet source** pour tester vos mappages avec quelques utilisateurs test d’Azure AD. Après avoir vérifié que les mappages fonctionnent, vous pouvez supprimer le filtre ou l'étendre progressivement pour inclure d'autres utilisateurs.

1. Le champ **Actions d’objet cible** prend uniquement en charge l’opération **Mise à jour**.

1. Dans la section **Mappages d’attributs**, vous pouvez uniquement modifier l’ID correspondant qui est utilisé pour lier un profil utilisateur SuccessFactors à un utilisateur Azure AD et l’attribut dans Azure AD qui sert de source d’email. 
    >[!div class="mx-imgBorder"]
    >![Portail Azure](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >La réécriture SuccessFactors prend uniquement en charge l’attribut email. N’utilisez pas **Ajouter un nouveau mappage** pour ajouter de nouveaux attributs. 

1. Pour enregistrer vos mappages, cliquez sur **Enregistrer** en haut de la section Mappage d'attributs.

Une fois vos mappages d'attributs configurés, vous pouvez [activer et lancer le service d'approvisionnement d'utilisateurs](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Activer et lancer l'approvisionnement des utilisateurs

Une fois les configurations d’application de provisionnement SuccessFactors effectuées, vous pouvez activer le service de provisionnement dans le portail Azure.

> [!TIP]
> Par défaut, lorsque vous activez le service d'approvisionnement, il lance les opérations d'approvisionnement pour tous les utilisateurs concernés. En cas d'erreur de mappage ou de problème lié aux données Workday, le travail d'approvisionnement peut échouer et être mis en quarantaine. Pour éviter ce genre de problème, nous vous recommandons de configurer le filtre **Portée de l'objet source** et de tester vos mappages d'attributs sur quelques utilisateurs test avant de lancer la synchronisation complète de tous les utilisateurs. Après avoir vérifié que les mappages fonctionnent et qu'ils vous donnent les résultats souhaités, vous pouvez supprimer le filtre ou l'étendre progressivement pour inclure d'autres utilisateurs.

1. Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Cette opération permet de lancer la synchronisation initiale, dont la durée dépendra du nombre d’utilisateurs du locataire SuccessFactors. Vous pouvez consulter la barre de progression pour suivre la progression du cycle de synchronisation. 

4. À tout moment, consultez l’onglet **Journaux d’audit** dans le portail Azure pour connaître les actions effectuées par le service d’approvisionnement. Les journaux d’audit répertorient tous les événements de synchronisation individuels effectués par le service d’approvisionnement, tels que les utilisateurs lus dans Workday et par la suite ajoutés ou mis à jour dans Active Directory. 

5. Au terme de la synchronisation initiale, un rapport de synthèse d'audit est créé dans l'onglet **Approvisionnement**, comme illustré ci-dessous.

   > [!div class="mx-imgBorder"]
   > ![Barre de progression de provisionnement](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
* [Découvrez comment configurer l’authentification unique entre SuccessFactors et Azure Active Directory](successfactors-tutorial.md)
* [Découvrez comment intégrer d’autres applications SaaS à Azure Active Directory](tutorial-list.md)
* [Découvrez comment exporter et importer vos configurations de provisionnement](../manage-apps/export-import-provisioning-configuration.md)

