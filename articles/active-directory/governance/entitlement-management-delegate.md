---
title: Déléguer des tâches dans la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez les rôles que vous pouvez affecter pour déléguer des tâches dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b631f078240821e79513c4bd944a33b4725bc52
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207140"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Déléguer des tâches dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Par défaut, les administrateurs généraux et les administrateurs d’utilisateurs peuvent créer et gérer tous les aspects de la gestion des droits d’utilisation Azure AD. Toutefois, les utilisateurs de ces rôles sont susceptibles de ne pas connaître tous les scénarios dans lesquels des packages d’accès sont requis. En général, ce sont les utilisateurs au sein des services qui savent qui a besoin de collaborer. 

Plutôt que d’accorder des autorisations illimitées pour aux non-administrateurs, vous pouvez accorder aux utilisateurs les dernières autorisations dont ils ont besoin pour effectuer leur travail et éviter de créer des conflits ou des droits d’accès inappropriés. Cet article décrit les rôles que vous pouvez affecter afin de déléguer des diverses tâches dans la gestion des droits d’utilisation. 

## <a name="delegate-example-for-departmental-adoption"></a>Exemple de délégation pour l’adoption par service

Pour comprendre comment déléguer des tâches de gestion des droits d’utilisation, prenons un exemple. 

Supposons que votre organisation possède les cinq utilisateurs suivants :

| Utilisateur | department | Notes |
| --- | --- | --- |
| Alice | IT | Administrateur général |
| Bob | Recherche | Bob est également propriétaire d’un groupe de recherche |
| Carole | Recherche |  |
| Dave | Marketing |  |
| Elisa | Marketing | Elisa est également propriétaire d’une application marketing |

Les services Recherche et Marketing souhaitent utiliser la gestion des droits d’utilisation pour leurs utilisateurs. Alice n’est pas encore prête que d’autres services utilisent la gestion des droits d’utilisation. Voici comment Alice pourrait déléguer des tâches aux services de Recherche et Marketing.

1. Alice crée un nouveau groupe de sécurité Azure AD pour les créateurs de catalogues et ajoute Bob, Carol, Dave et Elisa à ce groupe.

1. Alice utilise les paramètres de gestion des droits d’utilisation pour ajouter ce groupe au rôle de créateurs de catalogues.

1. Carol crée un catalogue **Recherche** et ajoute Bob comme copropriétaire de ce catalogue. Bob ajoute le groupe de recherche qu’il détient au catalogue en tant que ressource, de sorte qu’il puisse être utilisé dans un package d’accès pour la collaboration de recherche.

1. Dave crée un catalogue **Marketing** et ajoute Elisa comme copropriétaire de ce catalogue. Elisa ajoute l’application marketing qu’elle détient au catalogue en tant que ressource, de sorte qu’il puisse être utilisé dans un package d’accès pour la collaboration marketing.

Maintenant les services Recherche et Marketing peuvent utiliser la gestion des droits d’utilisation. Bob, Carol, Dave et Elisa peuvent créer et gérer des packages d’accès dans leurs catalogues respectifs.

![Exemple de délégation de la gestion des droits d’utilisation](./media/entitlement-management-delegate/elm-delegate.png)


## <a name="entitlement-management-roles"></a>Rôles de gestion des droits d’utilisation

La gestion des droits d’utilisation a les rôles suivants, qui sont spécifiques à la gestion des droits d’utilisation.

| Role | Description |
| --- | --- |
| Créateur de catalogue | Crée et gère des catalogues. Il s’agit, en général, d’un administrateur informatique qui n’est ni administrateur d’entreprise, ni propriétaire de ressource pour une collection de ressources. La personne qui crée un catalogue devient automatiquement le premier propriétaire du catalogue et peut ajouter des propriétaires de catalogues. |
| Propriétaire de catalogue | Modifie et gère les catalogues existants. Il s’agit, en général, d’un administrateur informatique, d’un propriétaire de ressource ou d’un utilisateur désigné par le propriétaire du catalogue. |
| Gestionnaire de package d’accès | Modifie et gère tous les packages d’accès existants au sein d’un catalogue. |

De plus, l’approbateur et le demandeur désignés d’un package d’accès ont également des droits, bien qu’il ne s’agisse pas de rôles.
 
* Approbateur : personne autorisée par une stratégie à approuver ou refuser des demandes d’accès aux packages, bien qu’elle ne puisse pas modifier les définitions de ces derniers.
* Demandeur : personne autorisée par la stratégie d’un package d’accès à demander ce package d’accès.

Le tableau suivant répertorie les tâches réalisables par ces rôles.

| Tâche | Créateur de catalogue | Propriétaire de catalogue | Gestionnaire de package d’accès | Approbateur |
| --- | :---: | :---: | :---: | :---: |
| [Créer un catalogue](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Ajouter une ressource à un catalogue](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Modifier un catalogue](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Supprimer un catalogue](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Ajouter un propriétaire de catalogue ou un gestionnaire de package d’accès à un catalogue](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Créer un package d’accès dans un catalogue](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Gérer des rôles de ressources dans un package d’accès](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Créer et modifier des stratégies](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Affecter directement un utilisateur à un package d’accès](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Afficher qui a une affectation à un package d’accès](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Afficher les requêtes d’un package d’accès](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Afficher les erreurs de remise d’une requête](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Annuler une requête en attente](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Masquer un package d’accès](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Supprimer un package d’accès](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Approuver une requête d’accès](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Rôles requis pour ajouter des ressources à un catalogue

Un administrateur d’entreprise peut ajouter ou supprimer n’importe quel groupe (groupes de sécurité ou groupes Office 365 créés dans le cloud), application ou site SharePoint Online d’un catalogue. Un administrateur d’utilisateurs peut ajouter ou supprimer n’importe quel groupe ou application d’un catalogue.

Afin qu’un utilisateur, qui ni administrateur général, ni administrateur d’utilisateurs, puisse ajouter des groupes, des applications ou des sites SharePoint Online à un catalogue, cet utilisateur doit disposer *à la fois* du rôle Azure AD Directory requis et du rôle de gestion des droits d’utilisation de propriétaire de catalogue. Le tableau suivant répertorie les combinaisons de rôles nécessaires pour ajouter des ressources à un catalogue. Pour supprimer les ressources d’un catalogue, vous devez disposer des mêmes rôles.

| Rôle d’annuaire Azure AD | Rôle de gestion des droits d’utilisation | Peut ajouter un groupe de sécurité | Peut ajouter un groupe Office 365 | Peut ajouter une application | Peut ajouter un site SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrateur général](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrateur d’utilisateurs](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrateur Intune](../users-groups-roles/directory-assign-admin-roles.md) | Propriétaire de catalogue | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrateur Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Propriétaire de catalogue |  | :heavy_check_mark: |  |  |
| [Administrateur du service Teams](../users-groups-roles/directory-assign-admin-roles.md) | Propriétaire de catalogue |  | :heavy_check_mark: |  |  |
| [Administrateur SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Propriétaire de catalogue |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrateur d’application](../users-groups-roles/directory-assign-admin-roles.md) | Propriétaire de catalogue |  |  | :heavy_check_mark: |  |
| [Administrateur d’application cloud](../users-groups-roles/directory-assign-admin-roles.md) | Propriétaire de catalogue |  |  | :heavy_check_mark: |  |
| Utilisateur | Propriétaire de catalogue | Seulement si propriétaire d’un groupe | Seulement si propriétaire d’un groupe | Seulement si propriétaire d’une application |  |

Pour déterminer le rôle le moins privilégié pour une tâche, vous pouvez également référencer les [rôles d’administrateur par tâche administrateur dans Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="add-a-catalog-creator"></a>Ajouter un créateur de catalogue

Si vous souhaitez déléguer la création de catalogues, ajoutez des utilisateurs au rôle de créateur de catalogue.  Vous pouvez ajouter des utilisateurs individuels, ou pour plus de commodité un groupe, dont les membres sont alors en mesure de créer des catalogues. Suivez ces étapes pour affecter un utilisateur au rôle de créateur de catalogue.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, dans la section **Gestion des droits d’utilisation**, cliquez sur **Paramètres**.

1. Cliquez sur **Modifier**.

1. Dans la section **Déléguer la gestion des droits d’utilisation**, cliquez sur **Ajouter des créateurs de catalogue** pour sélectionner les utilisateurs ou les groupes qui seront membres de ce rôle de gestion des droits d’utilisation.

1. Cliquez sur **Sélectionner**.

1. Cliquez sur **Enregistrer**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Ajouter un propriétaire de catalogue ou un gestionnaire de package d’accès

Si vous souhaitez déléguer la gestion d’un catalogue ou de packages accès dans le catalogue, ajoutez des utilisateurs aux rôles de propriétaire de catalogue ou de gestionnaire de package d’accès. La personne qui crée un catalogue en devient le premier propriétaire. Suivez ces étapes pour affecter un utilisateur au rôle de propriétaire de catalogue ou de gestionnaire de package d’accès.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou propriétaire de catalogue

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Catalogues**, puis ouvrez le catalogue auquel vous souhaitez ajouter des administrateurs.

1. Dans le menu de gauche, cliquez sur **Rôles et administrateurs**.

1. Cliquez sur **Ajouter des propriétaires** ou **Ajouter des gestionnaires de package d’accès** pour sélectionner les membres de ces rôles.

1. Cliquez sur **Sélectionner** pour ajouter ces membres.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des approbateurs](entitlement-management-access-package-edit.md#policy-request)
- [Ajouter des ressources à un catalogue](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
