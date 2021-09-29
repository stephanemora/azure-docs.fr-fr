---
title: Délégation et rôles dans la gestion des droits d’utilisation - Azure AD
description: Découvrez comment déléguer la gouvernance des accès des administrateurs informatiques aux chefs de service et chefs de projet pour qu’ils puissent gérer les accès eux-mêmes.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 7/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 864633246a673e331e65a1b9de329b9528ab701f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814582"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Délégation et rôles dans la gestion des droits d’utilisation Azure AD

Par défaut, les administrateurs généraux et les administrateurs de la gouvernance des identités peuvent créer et gérer tous les aspects de la gestion des droits d’utilisation Azure AD. Toutefois, les utilisateurs de ces rôles sont susceptibles de ne pas connaître toutes les situations dans lesquelles des packages d’accès sont exigés. D’ordinaire, il s’agit d’utilisateurs au sein des services, équipes ou projets respectifs qui savent avec qui ils collaborent, en utilisant quelles ressources et pendant combien de temps. Plutôt que d’accorder des autorisations illimitées aux non-administrateurs, vous pouvez accorder aux utilisateurs les autorisations minimales dont ils ont besoin pour effectuer leur travail et éviter de créer des conflits ou des droits d’accès inappropriés.

Cette vidéo fournit une vue d’ensemble de la délégation de la gouvernance des accès de l’administrateur informatique aux utilisateurs qui ne sont pas administrateurs.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Exemple de délégué

Pour comprendre comment déléguer la gouvernance des accès dans le cadre de la gestion des droits d’utilisation, prenons un exemple. Supposons que votre organisation emploie l’administrateur et les chefs de service suivants.

![Déléguer de l’administrateur informatique aux chefs](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

En tant qu’administrateur informatique, Hana a des contacts au sein de chaque service : Mamta du service marketing, Mark du service financier et Joe du service juridique, chacun étant responsable des ressources et du contenu stratégique métier de son service.

Avec la gestion des droits d’utilisation, vous pouvez déléguer la gouvernance des accès à ces non-administrateurs, car il s’agit des personnes qui savent quels utilisateurs ont besoin d’un accès, pendant combien de temps et à quelles ressources. Cette délégation à des non-administrateurs garantit que ce sont les bonnes personnes qui gèrent les accès pour leurs services.

Voici une façon pour Hana de déléguer la gouvernance des accès aux services marketing, financier et juridique.

1. Hana crée un groupe de sécurité Azure AD et ajoute Mamta, Mark et Joe en tant que membres du groupe.

1. Hana ajoute ce groupe au rôle de créateur de catalogue.

    Mamta, Mark et Joe peuvent désormais créer des catalogues pour leurs services, ajouter les ressources dont leurs services ont besoin et effectuer d’autres actions de délégation au sein de leur catalogue. Ils ne peuvent pas voir les catalogues des autres.

1. Mamta crée un catalogue **Marketing**, qui est un conteneur de ressources.

1. Mamta ajoute les ressources que possède son service marketing à ce catalogue.

1. Mamta peut ajouter d’autres personnes de son service comme propriétaires de ce catalogue, ce qui permet de partager les responsabilités de la gestion du catalogue.

1. Mamta peut également déléguer la création et la gestion des packages d’accès dans le catalogue Marketing aux chefs de projet du service marketing. Pour cela, elle peut leur attribuer le rôle de gestionnaire de package d’accès. Un gestionnaire de package d’accès peut créer et gérer des packages d’accès. 

Le diagramme suivant montre les catalogues contenant les ressources des services marketing, financier et juridique. À l’aide de ces catalogues, les chefs de projet peuvent créer des packages d’accès pour leurs équipes ou projets.

![Exemple de délégation de la gestion des droits d’utilisation](./media/entitlement-management-delegate/elm-delegate.png)

À l’issue de la délégation, le service marketing peut avoir des rôles similaires à ceux indiqués dans le tableau suivant.

| Utilisateur | Fonction principale | Rôle Azure AD | Rôle de gestion des droits d’utilisation |
| --- | --- | --- | --- |
| Hana | Administrateur informatique | Administrateur général ou administrateur de la gouvernance des identités  |  |
| Mamta | Directrice marketing | Utilisateur | Créateur du catalogue et propriétaire du catalogue |
| Bob | Responsable marketing | Utilisateur | Propriétaire de catalogue |
| Jessica | Chef de projet marketing | Utilisateur | Gestionnaire de package d’accès |

## <a name="entitlement-management-roles"></a>Rôles de gestion des droits d’utilisation

La gestion des droits d’utilisation a les rôles suivants, qui sont spécifiques à la gestion des droits d’utilisation.

| Rôle de gestion des droits d’utilisation | ID de définition de rôle | Description |
| --- | --- | -- |
| Créateur de catalogue | `ba92d953-d8e0-4e39-a797-0cbedb0a89e8` | Crée et gère des catalogues. Il s’agit, en général, d’un administrateur informatique qui n’est ni Administrateur d’entreprise, ni propriétaire de ressource pour une collection de ressources. La personne qui crée un catalogue devient automatiquement le premier propriétaire du catalogue et peut ajouter des propriétaires de catalogues. Un créateur de catalogue ne peut ni gérer ni voir les catalogues dont il n’est pas propriétaire, et ne peut pas ajouter à un catalogue les ressources dont il n’est pas propriétaire. Si le créateur de catalogue doit gérer un autre catalogue ou ajouter des ressources dont il n’est pas propriétaire, il peut demander à être copropriétaire de ce catalogue ou de cette ressource. |
| Propriétaire de catalogue | `ae79f266-94d4-4dab-b730-feca7e132178` | Modifie et gère les catalogues existants. Il s’agit, en général, d’un administrateur informatique, d’un propriétaire de ressource ou d’un utilisateur désigné par le propriétaire du catalogue. |
| Lecteur du catalogue | `44272f93-9762-48e8-af59-1b5351b1d6b3` | Affichez les packages d’accès existants dans un catalogue. |
| Gestionnaire de package d’accès | `7f480852-ebdc-47d4-87de-0d8498384a83` | Modifie et gère tous les packages d’accès existants au sein d’un catalogue. |
| Gestionnaire d'attribution de package d'accès | `e2182095-804a-4656-ae11-64734e9b7ae5` | Modifie et gère toutes les affectations de packages d’accès existantes. |

De plus, l’approbateur choisi et un demandeur d’un package d’accès ont des droits, bien qu’il ne s’agisse pas de rôles.

| Right | Description |
| --- | --- |
| Approbateur | Personne autorisée par une stratégie à approuver ou refuser des demandes de packages d’accès, bien qu’elle ne puisse pas modifier les définitions de ces derniers. |
| Demandeur | personne autorisée par la stratégie d’un package d’accès à demander ce package d’accès. |

Le tableau suivant liste les tâches que les rôles de gestion des droits d’utilisation peuvent effectuer.

| Tâche | Admin | Créateur de catalogue | Propriétaire de catalogue | Gestionnaire de package d’accès | Gestionnaire d'attribution de package d'accès |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Déléguer à un créateur de catalogue](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Ajouter une organisation connectée](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Créer un catalogue](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Ajouter une ressource à un catalogue](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Ajouter un propriétaire de catalogue](entitlement-management-catalog-create.md#add-more-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Modifier un catalogue](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Supprimer un catalogue](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Déléguer à un gestionnaire de package d’accès](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Supprimer un gestionnaire de package d’accès](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Créer un package d’accès dans un catalogue](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Modifier des rôles de ressources dans un package d’accès](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Créer et modifier des stratégies](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Affecter directement un utilisateur à un package d’accès](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Supprimer directement un utilisateur d’un package d’accès](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Afficher qui a une affectation à un package d’accès](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Afficher les requêtes d’un package d’accès](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Afficher les erreurs de remise d’une requête](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Retraiter une demande](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Annuler une requête en attente](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Masquer un package d’accès](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Supprimer un package d’accès](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Rôles requis pour ajouter des ressources à un catalogue

Un Administrateur d’entreprise peut ajouter ou supprimer n’importe quel groupe (groupes de sécurité ou groupes Microsoft 365 créés dans le cloud), application ou site SharePoint Online d’un catalogue. Un administrateur d’utilisateurs peut ajouter ou supprimer tout groupe ou toute application dans un catalogue, à l’exception d’un groupe configuré comme étant assignable à un rôle d’annuaire.  Pour plus d’informations sur les groupes avec attribution de rôle, consultez [Créer un groupe avec attribution de rôle dans Azure Active Directory](../roles/groups-create-eligible.md).

> [!NOTE]
> Les utilisateurs qui se sont vu attribuer le rôle d’administrateur d’utilisateurs ne pourront plus créer de catalogues ni gérer les packages d’accès dans un catalogue dont ils ne sont pas propriétaires. Si le rôle d’administrateur d’utilisateurs de votre organisation a été attribué à des utilisateurs pour configurer des catalogues, des packages d’accès ou des stratégies de la gestion des droits d’utilisation, vous devez attribuer à ces utilisateurs le **rôle d’administrateur de gouvernance des identités**.

Pour qu’un utilisateur qui n’est pas administrateur général puisse ajouter des groupes, des applications ou des sites SharePoint Online à un catalogue, il doit disposer *à la fois* d’un rôle d’annuaire Azure AD ou de la propriété de la ressource, et du rôle de gestion des droits d’utilisation du propriétaire du catalogue. Le tableau suivant répertorie les combinaisons de rôles nécessaires pour ajouter des ressources à un catalogue. Pour supprimer les ressources d’un catalogue, vous devez disposer des mêmes rôles.

| Rôle d’annuaire Azure AD | Rôle de gestion des droits d’utilisation | Peut ajouter un groupe de sécurité | Peut ajouter un groupe Microsoft 365 | Peut ajouter une application | Peut ajouter un site SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrateur général](../roles/permissions-reference.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrateur d’utilisateurs](../roles/permissions-reference.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrateur Intune](../roles/permissions-reference.md) | Propriétaire de catalogue | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrateur Exchange](../roles/permissions-reference.md) | Propriétaire de catalogue |  | :heavy_check_mark: |  |  |
| [Administrateur du service Teams](../roles/permissions-reference.md) | Propriétaire de catalogue |  | :heavy_check_mark: |  |  |
| [Administrateur SharePoint](../roles/permissions-reference.md) | Propriétaire de catalogue |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrateur d’application](../roles/permissions-reference.md) | Propriétaire de catalogue |  |  | :heavy_check_mark: |  |
| [Administrateur d’application cloud](../roles/permissions-reference.md) | Propriétaire de catalogue |  |  | :heavy_check_mark: |  |
| Utilisateur | Propriétaire de catalogue | Seulement si propriétaire d’un groupe | Seulement si propriétaire d’un groupe | Seulement si propriétaire d’une application |  |

Pour déterminer le rôle le moins privilégié pour une tâche, vous pouvez également référencer les [rôles d’administrateur par tâche administrateur dans Azure Active Directory](../roles/delegate-by-task.md#entitlement-management).

## <a name="manage-role-assignments-programmatically-preview"></a>Gérer les attributions de rôles par programmation (préversion)

Vous pouvez également afficher et mettre à jour les créateurs de catalogue ainsi que les attributions de rôles propres au catalogue de gestion des droits d’utilisation à l’aide de Microsoft Graph.  Un utilisateur disposant d’un rôle approprié et d’une application qui a l’autorisation déléguée `EntitlementManagement.ReadWrite.All` peut appeler l’API Graph pour [lister les définitions de rôles](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true) de la gestion des droits d’utilisation et [lister les attributions de rôles](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true) de ces définitions de rôles.

Par exemple, pour voir les rôles propres à la gestion des droits d’utilisation qui ont été attribués à un utilisateur ou à un groupe, utilisez la requête Graph pour lister les attributions de rôles, puis indiquez l’ID de l’utilisateur ou du groupe comme valeur du filtre de requête `principalId` :

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=principalId eq '10850a21-5283-41a6-9df3-3d90051dd111'&$expand=roleDefinition&$select=id,appScopeId,roleDefinition
```

Dans le cas d’un rôle propre à un catalogue, le `appScopeId` de la réponse indique le catalogue dans lequel l’utilisateur se voit attribuer un rôle.  Notez que cette réponse récupère uniquement les affectations explicites de ce principal à des rôles dans la gestion des droits d’utilisation, mais qu’elle ne retourne pas de résultats pour un utilisateur qui dispose de droits d’accès via un rôle d’annuaire ou via l’appartenance à un groupe affecté à un rôle.


## <a name="next-steps"></a>Étapes suivantes

- [Déléguer la gouvernance des accès aux créateurs de catalogue](entitlement-management-delegate-catalog.md)
- [Déléguer la gouvernance des accès aux gestionnaires de package d’accès](entitlement-management-delegate-managers.md)
- [Créer et gérer un catalogue de ressources](entitlement-management-catalog-create.md)
