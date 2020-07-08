---
title: Délégation et rôles dans la gestion des droits d’utilisation - Azure AD
description: Découvrez comment déléguer la gouvernance des accès des administrateurs informatiques aux chefs de service et chefs de projet pour qu’ils puissent gérer les accès eux-mêmes.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84692136"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Délégation et rôles dans la gestion des droits d’utilisation Azure AD

Par défaut, les administrateurs généraux et les administrateurs d’utilisateurs peuvent créer et gérer tous les aspects de la gestion des droits d’utilisation Azure AD. Toutefois, les utilisateurs de ces rôles sont susceptibles de ne pas connaître toutes les situations dans lesquelles des packages d’accès sont exigés. D’ordinaire, il s’agit d’utilisateurs au sein des services, équipes ou projets respectifs qui savent avec qui ils collaborent, en utilisant quelles ressources et pendant combien de temps. Plutôt que d’accorder des autorisations illimitées pour aux non-administrateurs, vous pouvez accorder aux utilisateurs les dernières autorisations dont ils ont besoin pour effectuer leur travail et éviter de créer des conflits ou des droits d’accès inappropriés.

Cette vidéo fournit une vue d’ensemble de la délégation de la gouvernance des accès de l’administrateur informatique aux utilisateurs qui ne sont pas administrateurs.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Exemple de délégué

Pour comprendre comment déléguer la gouvernance des accès dans le cadre de la gestion des droits d’utilisation, prenons un exemple. Supposons que votre organisation emploie l’administrateur et les chefs de service suivants.

![Déléguer de l’administrateur informatique aux chefs](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

En tant qu’administrateur informatique, Hana a des contacts au sein de chaque service : Mamta du service marketing, Mark du service financier et Joe du service juridique, chacun étant responsable des ressources et du contenu stratégique métier de son service.

Avec la gestion des droits d’utilisation, vous pouvez déléguer la gouvernance des accès à ces non-administrateurs, car il s’agit des personnes qui savent quels utilisateurs ont besoin d’un accès, pendant combien de temps et à quelles ressources. Ainsi, ce sont les bonnes personnes qui gèrent les accès pour leurs services.

Voici une façon pour Hana de déléguer la gouvernance des accès aux services marketing, financier et juridique.

1. Hana crée un groupe de sécurité Azure AD et ajoute Mamta, Mark et Joe en tant que membres du groupe.

1. Hana ajoute ce groupe au rôle de créateur de catalogue.

    Mamta, Mark et Joe peuvent désormais créer des catalogues pour leurs services, ajouter les ressources dont leurs services ont besoin et effectuer d’autres actions de délégation au sein de leur catalogue.

    Notez que Mamta, Mark et Joe ne peuvent pas voir les catalogues des autres.

1. Mamta crée un catalogue **Marketing**, qui est un conteneur de ressources.

1. Mamta ajoute les ressources que possède son service marketing à ce catalogue.

1. Mamta peut aussi y ajouter d’autres personnes de son service comme propriétaires de catalogue. Ainsi, les responsabilités de la gestion du catalogue sont mieux partagées.

1. Mamta peut également déléguer la création et la gestion des packages d’accès dans le catalogue Marketing aux chefs de projet du service marketing. Pour cela, elle peut leur attribuer le rôle de gestionnaire de package d’accès. Un gestionnaire de package d’accès peut créer et gérer des packages d’accès. 

Le diagramme suivant montre les catalogues contenant les ressources des services marketing, financier et juridique. À l’aide de ces catalogues, les chefs de projet peuvent créer des packages d’accès pour leurs équipes ou projets.

![Exemple de délégation de la gestion des droits d’utilisation](./media/entitlement-management-delegate/elm-delegate.png)

À l’issue de la délégation, le service marketing peut avoir des rôles similaires à ceux indiqués dans le tableau suivant.

| Utilisateur | Fonction principale | Rôle Azure AD | Rôle de gestion des droits d’utilisation |
| --- | --- | --- | --- |
| Hana | Administrateur informatique | Administrateur général ou Administrateur d’utilisateurs |  |
| Mamta | Directrice marketing | Utilisateur | Créateur du catalogue et propriétaire du catalogue |
| Bob | Responsable marketing | Utilisateur | Propriétaire de catalogue |
| Jessica | Chef de projet marketing | Utilisateur | Gestionnaire de package d’accès |

## <a name="entitlement-management-roles"></a>Rôles de gestion des droits d’utilisation

La gestion des droits d’utilisation a les rôles suivants, qui sont spécifiques à la gestion des droits d’utilisation.

| Rôle de gestion des droits d’utilisation | Description |
| --- | --- |
| Créateur de catalogue | Crée et gère des catalogues. Il s’agit, en général, d’un administrateur informatique qui n’est ni administrateur d’entreprise, ni propriétaire de ressource pour une collection de ressources. La personne qui crée un catalogue devient automatiquement le premier propriétaire du catalogue et peut ajouter des propriétaires de catalogues. Un créateur de catalogue ne peut ni gérer ni voir les catalogues dont il n’est pas propriétaire, et ne peut pas ajouter à un catalogue les ressources dont il n’est pas propriétaire. Si le créateur de catalogue doit gérer un autre catalogue ou ajouter des ressources dont il n’est pas propriétaire, il peut demander à être copropriétaire de ce catalogue ou de cette ressource. |
| Propriétaire de catalogue | Modifie et gère les catalogues existants. Il s’agit, en général, d’un administrateur informatique, d’un propriétaire de ressource ou d’un utilisateur désigné par le propriétaire du catalogue. |
| Gestionnaire de package d’accès | Modifie et gère tous les packages d’accès existants au sein d’un catalogue. |

De plus, l’approbateur et le demandeur désignés d’un package d’accès ont également des droits, bien qu’il ne s’agisse pas de rôles.

| Right | Description |
| --- | --- |
| Approbateur | personne autorisée par une stratégie à approuver ou refuser des demandes d’accès aux packages, bien qu’elle ne puisse pas modifier les définitions de ces derniers. |
| Demandeur | personne autorisée par la stratégie d’un package d’accès à demander ce package d’accès. |

Le tableau suivant liste les tâches que les rôles de gestion des droits d’utilisation peuvent effectuer.

| Tâche | Admin | Créateur de catalogue | Propriétaire de catalogue | Gestionnaire de package d’accès |
| --- | :---: | :---: | :---: | :---: |
| [Déléguer à un créateur de catalogue](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Ajouter une organisation connectée](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Créer un catalogue](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Ajouter une ressource à un catalogue](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Ajouter un propriétaire de catalogue](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Modifier un catalogue](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Supprimer un catalogue](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Déléguer à un gestionnaire de package d’accès](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Supprimer un gestionnaire de package d’accès](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Créer un package d’accès dans un catalogue](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Modifier des rôles de ressources dans un package d’accès](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Créer et modifier des stratégies](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Affecter directement un utilisateur à un package d’accès](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Afficher qui a une affectation à un package d’accès](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Afficher les requêtes d’un package d’accès](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Afficher les erreurs de remise d’une requête](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Retraiter une demande](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Annuler une requête en attente](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Masquer un package d’accès](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Supprimer un package d’accès](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Rôles requis pour ajouter des ressources à un catalogue

Un Administrateur d’entreprise peut ajouter ou supprimer n’importe quel groupe (groupes de sécurité ou groupes Office 365 créés dans le cloud), application ou site SharePoint Online d’un catalogue. Un administrateur d’utilisateurs peut ajouter ou supprimer n’importe quel groupe ou application d’un catalogue.

Afin qu’un utilisateur, qui ni administrateur général, ni administrateur d’utilisateurs, puisse ajouter des groupes, des applications ou des sites SharePoint Online à un catalogue, cet utilisateur doit disposer *à la fois* du rôle Azure AD Directory requis et du rôle de gestion des droits d’utilisation de propriétaire de catalogue. Le tableau suivant répertorie les combinaisons de rôles nécessaires pour ajouter des ressources à un catalogue. Pour supprimer les ressources d’un catalogue, vous devez disposer des mêmes rôles.

| Rôle d’annuaire Azure AD | Rôle de gestion des droits d’utilisation | Peut ajouter un groupe de sécurité | Peut ajouter un groupe Office 365 | Peut ajouter une application | Peut ajouter un site SharePoint Online |
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

## <a name="next-steps"></a>Étapes suivantes

- [Déléguer la gouvernance des accès aux créateurs de catalogue](entitlement-management-delegate-catalog.md)
- [Créer et gérer un catalogue de ressources](entitlement-management-catalog-create.md)
