---
title: Définir les rôles RBAC pour un accès administratif à Azure dans le portail - Recherche Azure
description: Contrôle d’accès en fonction du rôle (RBAC) sur le portail Azure pour le contrôle et la délégation de tâches d’administration dans le cadre de gestion de Recherche Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 34def35eba1e5c1645e6e1f9a505704d153ac716
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277765"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Définir des rôles RBAC pour l’accès des administrateurs

Azure offre un [modèle d’autorisation par rôle global](../role-based-access-control/role-assignments-portal.md) pour tous les services gérés via le portail ou les API Resource Manager. Les rôles Propriétaire, Contributeur et Lecteur définissent le niveau *d’administration des services* pour les utilisateurs, les groupes et les principaux de sécurité Active Directory assignés à chaque rôle. 

> [!Note]
> Il n’existe aucun contrôle d’accès en fonction du rôle pour sécuriser des parties d’un index ou d’un sous-ensemble de documents. Pour un accès en fonction de l’identité sur les résultats de recherche, vous pouvez créer des filtres de sécurité pour ajuster les résultats par identité, en supprimant les documents auxquels le demandeur ne doit pas avoir accès. Pour plus d’informations, consultez [Filtres de sécurité](search-security-trimming-for-azure-search.md) et [Sécuriser avec Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tâches de gestion par rôle

Pour Recherche Azure, les rôles sont associés à des niveaux d’autorisation qui prennent en charge les tâches de gestion suivantes :

| Rôle | Tâche |
| --- | --- |
| Propriétaire |Création ou suppression du service ou de tout objet sur le service, y compris les clés API, les index, les indexeurs, les sources de données d’indexeur et les planifications de l’indexeur.<p>Afficher l’état du service, notamment des compteurs et la taille du stockage.<p>Ajout ou suppression d'appartenance à un rôle (seul un Propriétaire peut gérer l'appartenance à un rôle).<p>Les administrateurs d’abonnement et de service appartiennent automatiquement au rôle Propriétaire. |
| Contributeur |Même niveau d’accès que le Propriétaire, à l’exception de la gestion des rôles RBAC. Par exemple, un Contributeur peut créer ou supprimer des objets ou afficher et régénérer des [clés API](search-security-api-keys.md), mais il ne peut pas modifier l’appartenance aux rôles. |
| [Rôle intégré contributeur de Service de recherche](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Équivalent au rôle de contributeur. |
| Lecteur |Affichez les bases et les métriques du service. Les membres de ce rôle ne peuvent pas afficher l’index, l’indexeur, la source de données ni les informations clés.  |

Les rôles n’accordent pas de droits d’accès au point de terminaison de service. Les opérations du service Search telles que la gestion ou le remplissage d'index, tout comme les requêtes de données de recherche, sont contrôlées via des clés api, et non par des rôles. Pour plus d’informations, consultez [Gérer des clés API](search-security-api-keys.md).

## <a name="see-also"></a>Voir aussi

+ [Gérer à l’aide de PowerShell](search-manage-powershell.md) 
+ [Performances et optimisation dans recherche Azure](search-performance-optimization.md)
+ [Découvrir le contrôle d’accès en fonction du rôle dans le portail Azure](../role-based-access-control/overview.md).