---
title: Contrôle d’accès en fonction du rôle dans Azure Cosmos DB avec l’intégration d’Azure Active Directory
description: Découvrez comment Azure Cosmos DB offre une protection de base de données avec l’intégration Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e43341e59e807fdc20033d909beadb9d582149b5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078939"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Contrôle d’accès en fonction du rôle dans Azure Cosmos DB

Azure Cosmos DB fournit un contrôle intégré accès en fonction du rôle (RBAC) pour les scénarios courants de gestion dans Azure Cosmos DB. Personne disposant d’un profil dans Azure Active Directory peut affecter ces rôles RBAC pour les utilisateurs, les groupes, les principaux du service, ou géré les identités pour accorder ou refuser l’accès aux ressources et opérations sur les ressources Azure Cosmos DB. Attributions de rôles sont limitées à un plan de contrôle d’accès uniquement, qui inclut l’accès aux comptes Azure Cosmos, les bases de données, les conteneurs et offre (débit).

## <a name="built-in-roles"></a>Rôles intégrés

Les rôles intégrés pris en charge par Azure Cosmos DB sont les suivantes :

|**Rôle intégré**  |**Description**  |
|---------|---------|
|[Contributeur de comptes DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Gérer des comptes Azure Cosmos DB.  |
|[Lecteur de compte COSMOS DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Lire les données de comptes Azure Cosmos DB.        |
|[Opérateur de sauvegarde COSMOS](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Peut envoyer la demande de restauration pour une base de données Azure Cosmos ou un conteneur.       |
|[Opérateur de COSMOS DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Pouvez approvisionner des comptes Azure Cosmos, les bases de données et les conteneurs, mais ne peut pas accéder aux clés qui sont nécessaires pour accéder aux données.         |

> [!IMPORTANT]
> Prise en charge RBAC dans Azure Cosmos DB s’applique aux opérations de plan de contrôle uniquement. Opérations de plan de données sont sécurisées à l’aide de clés principales ou les jetons de ressource. Pour plus d’informations, consultez [sécuriser l’accès aux données dans Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestion des identités et des accès

Le **contrôle d’accès (IAM)** volet dans le portail Azure est utilisé pour configurer le contrôle d’accès en fonction du rôle sur les ressources Azure Cosmos. Les rôles sont appliqués aux utilisateurs, des groupes, des principaux de service et des identités gérées dans Active Directory. Vous pouvez utiliser les rôles intégrés ou des rôles personnalisés pour les personnes et groupes. La capture d’écran suivante montre l’intégration d’Active Directory (RBAC) à l’aide du contrôle d’accès (IAM) dans le portail Azure :

![Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Rôles personnalisés

Outre les rôles intégrés, les utilisateurs peuvent également créer [des rôles personnalisés](../role-based-access-control/custom-roles.md) dans Azure et appliquer ces rôles aux principaux de service pour tous les abonnements au sein de leur client Active Directory. Rôles personnalisés fournissent aux utilisateurs un moyen de créer des définitions de rôles RBAC avec un jeu personnalisé de ressource opérations du fournisseur. Pour savoir quelles opérations sont disponibles pour la création de rôles personnalisés pour Azure Cosmos DB, consultez [opérations du fournisseur de ressources Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est le contrôle d’accès en fonction du rôle (RBAC) pour les ressources Azure](../role-based-access-control/overview.md)
- [Rôles personnalisés pour les ressources Azure](../role-based-access-control/custom-roles.md)
- [Opérations du fournisseur de ressources Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
