---
title: Qu’est la gestion des droits Azure AD ? (Version préliminaire) - Azure Active Directory
description: Obtenir une vue d’ensemble de la gestion des habilitations Azure Active Directory et comment vous pouvez l’utiliser pour gérer l’accès aux groupes, les applications et sites SharePoint Online pour les utilisateurs internes et externes.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b9baa48c13e317ba3fb54d998ee8f125d2093c7
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921057"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Qu’est la gestion des droits Azure AD ? (Préversion)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les employés dans les organisations ont besoin d’accéder à différents groupes, les applications et sites pour effectuer leur travail. Il est difficile de gérer cet accès. Dans la plupart des cas, il n’existe aucune liste organisée de toutes les ressources qu’un utilisateur a besoin pour un projet. Le responsable de projet a une bonne compréhension des ressources nécessaires, les personnes impliquées et la durée pendant laquelle que le projet est valable. Toutefois, le responsable de projet en général n’a pas autorisé à approuver ou accorder l’accès à d’autres personnes. Ce scénario se complique lorsque vous tentez de travailler avec des personnes externes ou des entreprises.

Gestion des habilitations Azure Active Directory (Azure AD) peut vous aider à gérer l’accès aux groupes, des applications et des sites SharePoint Online pour les utilisateurs internes, ainsi que les utilisateurs en dehors de votre organisation.

## <a name="why-use-entitlement-management"></a>Pourquoi utiliser la gestion des habilitations ?

Les entreprises doivent souvent faire face les défis lors de la gestion des accès aux ressources telles que :

- Les utilisateurs peuvent ne pas savent quel accès doit leur être
- Les utilisateurs peuvent avoir des difficultés à localiser les personnes appropriées ou les ressources droite
- Une fois que les utilisateurs de trouver et obtenez l’accès à une ressource, ils peuvent conserver pour accéder plus que nécessaire à des fins professionnelles

Ces problèmes sont compliquent pour les utilisateurs qui ont besoin d’accéder à partir d’un autre annuaire, telles que les utilisateurs externes qui proviennent d’organisations de chaîne d’approvisionnement ou d’autres partenaires commerciaux. Exemple :

- Les organisations peuvent ne pas savoir toutes les personnes dans d’autres répertoires pour être en mesure de les inviter
- Même si les organisations ont été en mesure d’inviter ces utilisateurs, les organisations peuvent vous souvenez pas gérer l’ensemble de l’accès utilisateur cohérente

Gestion des droits Azure AD peut aider à relever ces défis.

## <a name="what-can-i-do-with-entitlement-management"></a>Que puis-je faire avec la gestion des habilitations ?

Voici certaines des fonctionnalités de gestion des habilitations :

- Créer des packages de ressources liées que les utilisateurs peuvent demander
- Définir des règles de façon à demander des ressources et lorsque l’accès arrive à expiration
- Régissent le cycle de vie de l’accès pour les utilisateurs internes et externes
- Déléguer la gestion des ressources
- Désigner des approbateurs pour approuver les demandes
- Créer des rapports pour effectuer le suivi de l’historique

Pour une vue d’ensemble de la gouvernance des identités et de gestion des habilitations, regardez la vidéo suivante de la conférence Ignite 2018 :

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Quelles ressources puis-je gérer ?

Voici les types de ressources que vous pouvez gérer l’accès à avec la gestion des droits :

- Groupes de sécurité Azure AD
- Groupes Office 365
- Applications d’entreprise AD Azure
- Applications SaaS
- Applications intégrées personnalisé
- Collections de sites SharePoint Online
- Sites SharePoint Online

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser la gestion des droits Azure AD (version préliminaire), vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

Pour plus d’informations, consultez [s’inscrire pour les éditions d’Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) ou [Enterprise Mobility + Security E5 essai](https://aka.ms/emse5trial).

Clouds spécialisés, tels que Azure Government, Azure Allemagne et Azure China 21Vianet, ne sont pas actuellement disponibles pour une utilisation dans cette version préliminaire.

## <a name="what-are-access-packages-and-policies"></a>Quelles sont les stratégies et les packages de l’accès ?

Gestion des habilitations introduit le concept d’un *package access*. Un package d’accès est un ensemble de toutes les ressources qu’un utilisateur a besoin pour travailler sur un projet ou d’effectuer leur travail. Les ressources comprennent l’accès aux groupes, des applications ou des sites. Accès aux packages sont utilisés pour régir l’accès pour vos employés internes et également les utilisateurs en dehors de votre organisation. Packages d’accès sont définies dans des conteneurs appelés *catalogues*.

Packages de l’accès est également incluent un ou plusieurs *stratégies*. Une stratégie définit les règles ou les guardrails pour accéder à un package d’accès. L’activation d’une stratégie impose que seuls les utilisateurs appropriés accès sont accordés, aux ressources appropriées et pour la bonne quantité de temps.

![Stratégies et le package de l’accès](./media/entitlement-management-overview/elm-overview-access-package.png)

Avec un package d’accès et ses stratégies, le Gestionnaire de package accès définit :

- Ressources
- Rôles utilisateurs avez besoin pour les ressources
- Les utilisateurs internes et les utilisateurs externes qui sont éligibles pour demander l’accès
- Processus d’approbation et les utilisateurs qui peuvent approuver ou refuser l’accès
- Durée de l’accès de l’utilisateur

Le diagramme suivant illustre les différents éléments dans Gestion des habilitations. Il montre deux exemples de lots accès.

- **Package d’accès 1** inclut un seul groupe en tant que ressource. L’accès est défini avec une stratégie qui active un ensemble d’utilisateurs dans le répertoire pour demander l’accès.
- **Package d’accès 2** inclut un groupe, une application et un site SharePoint Online en tant que ressources. L’accès est défini avec deux stratégies. La première stratégie autorise un ensemble d’utilisateurs dans le répertoire pour demander l’accès. La deuxième stratégie permet aux utilisateurs dans un répertoire externe demander l’accès.

![Vue d’ensemble de la gestion des droits](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Utilisateurs externes

Lorsque vous utilisez le [Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) inviter expérience, vous devez déjà connaître les adresses de messagerie des utilisateurs invités externes vous souhaitez importer dans votre répertoire de ressources et de travailler avec. Cela fonctionne parfaitement lorsque vous travaillez sur un projet à court terme ou plus petit et que vous connaissez déjà tous les participants, mais il est plus difficile à gérer si vous avez un grand nombre d’utilisateurs que vous souhaitez utiliser, ou si les participants changent au fil du temps.  Par exemple, vous pouvez travailler avec une autre organisation et atteigne un point de contact avec cette organisation, mais au fil du temps des utilisateurs supplémentaires à partir de cette organisation seront également besoin d’accéder.

Avec la gestion des droits, vous pouvez définir une stratégie qui autorise les utilisateurs d’organisations que vous spécifiez, et qui utilisent également Azure AD, pour être en mesure de demander un package d’accès. Vous pouvez spécifier si l’approbation est requise et la date d’expiration de l’accès. Si l’approbation est requise, vous pouvez également désigner en tant qu’approbateur un ou plusieurs utilisateurs à partir de l’organisation externe que vous avez précédemment invité - dans la mesure où ils sont susceptibles de connaître les utilisateurs externes à partir de leur organisation ont besoin d’accéder. Une fois que vous avez configuré le package de l’accès, vous pouvez envoyer un lien vers le package de l’accès à la personne à contacter à l’organisation externe. Ce contact peut partager avec d’autres utilisateurs dans l’organisation externe, et ils peuvent utiliser ce lien pour demander le package de l’accès.  Utilisateurs de cette organisation qui ont déjà été invités dans votre annuaire peuvent également utiliser ce lien.

Lorsqu’une demande est approuvée, gestion des habilitations configurera l’utilisateur avec l’accès nécessaire, ce qui peut inclure l’invitation de l’utilisateur si elles ne sont pas déjà dans votre répertoire. Azure AD crée automatiquement un compte B2B pour eux.  Notez qu’un administrateur peut avoir limité précédemment quelles organisations qui est autorisés pour la collaboration, en définissant un [B2B autoriser ou refuser les liste](../b2b/allow-deny-list.md) pour autoriser ou bloquer des invitations à d’autres organisations.  Si l’utilisateur n’est pas autorisée par la liste verte ou rouge, puis ils ne seront pas invités.

Étant donné que vous ne souhaitez pas l’accès de l’utilisateur externe à éternels, vous spécifiez une date d’expiration dans la stratégie, tels que 180 jours. Après 180 jours, si leur accès n’est pas renouvelé, gestion des habilitations supprimera tous les accès associé à ce package de l’accès.  Si l’utilisateur qui a été invitée via la gestion des habilitations n’a aucune autres affectations de package d’accès, puis lorsqu’ils perdent leur dernière attribution, son compte B2B sera bloqué à partir de la connexion pendant 30 jours et par la suite supprimé.  Cela empêche la prolifération des comptes inutiles.  

## <a name="terminology"></a>Terminologie

Pour mieux comprendre le gestion des habilitations et sa documentation, vous devez examiner les termes suivants.

| Terme ou concept | Description  |
| --- | --- |
| gestion des habilitations | Un service qui est attribué, révoque et administre les packages de l’accès. |
| package de l’accès | Une collection d’autorisations et les stratégies de ressources que les utilisateurs peuvent demander. Un package d’accès est toujours contenu dans un catalogue. |
| demande d’accès | Une demande à accéder à un package d’accès. Une demande passe généralement par un flux de travail. |
| policy | Un ensemble de règles qui définit le cycle de vie de l’accès, telles que la façon dont les utilisateurs ont accès, qui peuvent approuver, et la durée pendant laquelle les utilisateurs ont accès. Les exemples de stratégie comprennent l’accès des employés et l’accès externe. |
| catalogue | Un conteneur de ressources associées et leur accès aux packages. |
| Catalogue général | Catalogue intégré qui est toujours disponible. Pour ajouter des ressources dans le catalogue général, requiert certaines autorisations. |
| Ressource | Une ressource ou un service (par exemple, un groupe, une application ou un site) qui peut disposer des autorisations pour un utilisateur. |
| type de ressource | Le type de ressource, qui inclut les groupes, les applications et sites SharePoint Online. |
| rôle de ressource | Une collection d’autorisations associées à une ressource. |
| répertoire de ressources | Un répertoire qui a une ou plusieurs ressources à partager. |
| utilisateurs affectés | Une affectation d’un package de l’accès à un utilisateur ou un groupe. |
| enable | Le processus de mise à disposition un package de l’accès des utilisateurs à demander. |

## <a name="roles-and-permissions"></a>Rôles et autorisations

Gestion des habilitations a différents rôles basés sur la fonction.

| Rôle | Description  |
| --- | --- |
| [Administrateur de l’utilisateur](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) | Gérer tous les aspects de la gestion des habilitations.<br/>Créer des utilisateurs et groupes. |
| Créateur de catalogue | Créer et gérer des catalogues. En général, un administrateur ou propriétaire de la ressource. La personne qui crée un catalogue automatiquement devient propriétaire du catalogue premier du catalogue. |
| Propriétaire du catalogue | Modifier et gérer des catalogues existants. En général, un administrateur ou propriétaire de la ressource. |
| Gestionnaire de package de l’accès | Modifier et gérer tous les packages d’accès existants au sein d’un catalogue. |
| Approbateur | Approuver les demandes d’accéder aux packages. |
| Demandeur | Demander l’accès de packages. |

Le tableau suivant répertorie les autorisations pour chacun de ces rôles.

| Tâche | Utilisateur admin | Créateur de catalogue | Propriétaire du catalogue | Gestionnaire de package de l’accès | Approbateur |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Créer un nouveau package d’accès dans le catalogue général](entitlement-management-access-package-create.md) | :heavy_check_mark: |  :heavy_check_mark: |  |  |  |
| [Créer un nouveau package d’accès dans un catalogue](entitlement-management-access-package-create.md) | :heavy_check_mark: |   | :heavy_check_mark: |  |  |
| [Ajouter/supprimer des rôles de ressources vers/à partir d’un package d’accès](entitlement-management-access-package-edit.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Spécifiez qui peut demander un package d’accès](entitlement-management-access-package-edit.md#add-a-new-policy) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Attribuer directement un utilisateur à un package de l’accès](entitlement-management-access-package-edit.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Vue qui a une assignation à un package d’accès](entitlement-management-access-package-edit.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Afficher les demandes d’un package d’accès](entitlement-management-access-package-edit.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Afficher les erreurs de remise d’une requête](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Annuler une demande en attente](entitlement-management-access-package-edit.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Masquer un package d’accès](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Supprimer un package d’accès](entitlement-management-access-package-edit.md#delete) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Approuver une demande d’accès](entitlement-management-request-approve.md) |  |  |  |  | :heavy_check_mark: |
| [Créer un catalogue](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Ajouter/supprimer des ressources vers/depuis le catalogue général](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  |  |  |  |
| [Ajouter/supprimer des ressources vers ou à partir d’un catalogue](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Ajouter des propriétaires de catalogue ou accéder aux gestionnaires de packages](entitlement-management-catalog-create.md#add-catalog-owners-or-access-package-managers) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Modifier/supprimer un catalogue](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer votre premier package d’accès](entitlement-management-access-package-first.md)
- [Scénarios courants](entitlement-management-scenarios.md)
