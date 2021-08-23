---
title: Autoriser l'accès par le biais des rôles Azure
titleSuffix: Azure Cognitive Search
description: Contrôle d’accès en fonction du rôle Azure (Azure RBAC) sur le portail Azure pour le contrôle et la délégation de tâches d’administration dans le cadre de la gestion de Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/28/2021
ms.openlocfilehash: 4e79af03f4545e4af799c660314212d18dbc91ff
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110459961"
---
# <a name="authorize-access-through-azure-roles-in-azure-cognitive-search"></a>Autoriser l'accès par le biais des rôles Azure dans Recherche cognitive Azure

Azure offre un [modèle d’autorisation par rôle global](../role-based-access-control/role-assignments-portal.md) pour tous les services gérés via le portail ou les API Resource Manager. Le modèle d'autorisation fournit les rôles Propriétaire, Contributeur et Lecteur, qui définissent le niveau d'*administration des services* pour les utilisateurs, les groupes et les principaux de sécurité Active Directory attribués à chaque rôle. Le service Recherche cognitive utilise ces trois rôles afin d'autoriser l'accès pour l'administration du service de recherche.

Le service Recherche cognitive ne prend pas en charge ce qui suit :

+ [Rôles personnalisés](../role-based-access-control/custom-roles.md).
+ Contrôle d'accès en fonction du rôle (Azure RBAC) sur les opérations liées au contenu, comme la création ou l'interrogation d'un index ou de tout autre objet dans le service.

  L'autorisation permettant d'effectuer des opérations sur le contenu nécessite [une clé API d'administration ou une clé API de requête](search-security-api-keys.md).

> [!Note]
> Pour un accès aux résultats de recherche basé sur l'identité (parfois appelé sécurité au niveau des lignes), vous pouvez créer des filtres de sécurité afin d'ajuster les résultats par identité, en supprimant les documents auxquels le demandeur ne doit pas avoir accès. Pour plus d’informations, consultez [Filtres de sécurité ](search-security-trimming-for-azure-search.md).

## <a name="roles-used-in-cognitive-search"></a>Rôles utilisés dans Recherche cognitive

Pour Recherche cognitive Azure, les rôles sont associés à des niveaux d’autorisation qui prennent en charge les tâches de gestion suivantes :

| Role | Tâche |
| --- | --- |
| Propriétaire |Créer ou supprimer le service. Créer, mettre à jour ou supprimer tout objet du service : clés API, index, cartes de synonymes, indexeurs, sources de données des indexeurs et ensembles de compétences. </br></br>Accès complet à toutes les informations de service exposées sur le portail ou via l'API REST de gestion, Azure PowerShell ou Azure CLI. </br></br>Attribuer l'appartenance à un rôle.</br></br>Les administrateurs d’abonnement et de service appartiennent automatiquement au rôle Propriétaire. |
| Contributeur | Même niveau d'accès que le Propriétaire, moins les attributions de rôles. [Contributeur du service de recherche](../role-based-access-control/built-in-roles.md#search-service-contributor) est équivalent au rôle intégré de contributeur générique. |
| Lecteur | Accès limité à des informations de service partielles. Sur le portail, le rôle Lecteur permet d'accéder aux informations de la page de présentation du service, de la section Éléments principaux et de l'onglet Surveillance. Tous les autres onglets et pages sont inaccessibles. </br></br>Dans la section Éléments principaux : groupe de ressources, état, emplacement, nom et ID de l'abonnement, balises, URL, niveau tarifaire, réplicas, partitions et unités de recherche. </br></br>Dans l'onglet Surveillance, consultez les métriques de service : latence de recherche, pourcentage de demandes limitées, nombre moyen de requêtes par seconde. </br></br>L'utilisateur n'a pas accès à l'onglet Utilisation (stockage, nombre d'index ou d'indexeurs créés dans le service) ni à aucune information des onglets Index, Indexeurs, Sources de données, Ensembles de compétences ou Sessions de débogage. |

Les rôles n’accordent pas de droits d’accès au point de terminaison de service. Les opérations du service de recherche, telles que la gestion ou le remplissage d'index et les requêtes relatives aux données de recherche, sont contrôlées par le biais de clés API, et non de rôles. Pour plus d'informations, consultez [Gérer les clés API](search-security-api-keys.md).

## <a name="tasks-and-permission-requirements"></a>Spécifications relatives aux tâches et aux autorisations

Le tableau suivant récapitule les opérations autorisées dans Recherche cognitive Azure, en indiquant le rôle ou la clé qui déverrouille l'accès à une opération particulière.

+ L'appartenance Azure RBAC permet d'accéder aux pages du portail et aux tâches de gestion des services (créer, supprimer ou modifier un service ou ses clés API).

+ Les clés API sont créées après l’existence d’un service et s’appliquent aux opérations de contenu sur le service.

En outre, pour les opérations liées au contenu sur le portail, comme la création ou la suppression d'objets, l'accès complet à toutes les opérations et informations est pris en charge par l'appartenance explicite à un rôle (Propriétaire ou Contributeur), ainsi que par l'utilisation interne par le portail d'une clé d'administration. En d'autres termes, si vous créez ou chargez un index sur le portail, votre appartenance RBAC vous donne accès aux pages, mais le portail lui-même utilise une clé d'administration pour authentifier l'opération dans le service.

| Opération | Contrôlé par |
|-----------|-------------------------|
| Créer ou supprimer un service | Autorisations Azure RBAC : Propriétaire ou contributeur |
| Configurer la sécurité réseau (pare-feu IP) | Autorisations Azure RBAC : Propriétaire ou contributeur |
| Créer ou supprimer un point de terminaison privé | Autorisations Azure RBAC : Propriétaire ou contributeur |
| Implémenter des clés gérées par le client | Autorisations Azure RBAC : Propriétaire ou contributeur |
| Ajuster les réplicas ou les partitions | Autorisations Azure RBAC : Propriétaire ou contributeur|
| Gérer les clés d’administration ou de requête | Autorisations Azure RBAC : Propriétaire ou contributeur|
| Afficher les informations de service dans le portail ou une API de gestion | Autorisations Azure RBAC : Propriétaire, Contributeur ou Lecteur  |
| Afficher les informations d’objet et les métriques dans le portail ou une API de gestion | Autorisations Azure RBAC : Propriétaire ou contributeur |
| Créer, modifier et supprimer des objets du service : <br>Index et composants (y compris les définitions de l'analyseur, les profils de scoring et les options CORS), indexeurs, sources de données, ensembles de compétences, synonymes et suggesteurs | Clé d'administration si vous utilisez une API, plus Propriétaire ou Contributeur Azure RBAC si vous utilisez le portail |
| Interroger un index | Clé d'administration ou de requête si vous utilisez une API, plus Propriétaire ou Contributeur Azure RBAC si vous utilisez le portail |
| Interroger des informations système à propos d’objets, telles que l’obtention de statistiques, de comptes et de listes d’objets | Clé d'administration si vous utilisez une API, plus Propriétaire ou Contributeur Azure RBAC si vous utilisez le portail |

## <a name="next-steps"></a>Étapes suivantes

+ [Gestion à l’aide de PowerShell](search-manage-powershell.md) 
+ [Performances et optimisation dans Recherche cognitive Azure](search-performance-optimization.md)
+ [Présentation du contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md).
