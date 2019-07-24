---
title: Contrôle d’accès basé sur les rôles dans Azure Cosmos DB avec intégration d’Azure Active Directory
description: Découvrez comment Azure Cosmos DB assure la protection des bases de données grâce à l’intégration d’Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 971d2ec96906a3309963495dd1af5d293a71f265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243513"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Contrôle d’accès en fonction du rôle (RBAC) dans Azure Cosmos DB

Azure Cosmos DB fournit un contrôle d’accès en fonction du rôle (RBAC) intégré pour les scénarios de gestion courants dans Azure Cosmos DB. Une personne disposant d’un profil dans Azure Active Directory peut assigner ces rôles RBAC à des utilisateurs, des groupes, des principaux de services ou des identités gérées pour accorder ou refuser l’accès aux ressources et aux opérations sur les ressources de Azure Cosmos DB. L’attribution des rôles est limitée au seul accès au plan de contrôle, ce qui inclut l’accès aux comptes, bases de données, conteneurs et offres (débit) d’Azure Cosmos.

## <a name="built-in-roles"></a>Rôles intégrés

Voici les rôles intégrés pris en charge par Azure Cosmos DB :

|**Rôle intégré**  |**Description**  |
|---------|---------|
|[Contributeur de compte DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Gérer des comptes Azure Cosmos DB.  |
|[Lecteur de compte Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Lire les données de comptes Azure Cosmos DB.        |
|[Opérateur de sauvegarde Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Peut envoyer une requête de restauration d’une base de données Azure Cosmos ou d’un conteneur.       |
|[Opérateur Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Peut provisionner des comptes, des bases de données et des conteneurs Azure Cosmos, mais pas d’accéder aux clés nécessaires pour accéder aux données.         |

> [!IMPORTANT]
> La prise en charge du RBAC dans Azure Cosmos DB s’applique uniquement aux opérations de plan de contrôle. Les opérations du plan de données sont sécurisées à l’aide de clés principales ou de jetons de ressources. Pour en savoir plus, veuillez consulter [Sécuriser l’accès aux données dans Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestion des identités et des accès

Le volet **Contrôle d’accès (IAM)** du Portail Microsoft Azure permet de configurer le contrôle d’accès en fonction du rôle sur les ressources Azure Cosmos. Les rôles sont appliqués aux utilisateurs, aux groupes, aux principaux de service et aux identités gérées dans Active Directory. Vous pouvez utiliser des rôles intégrés ou des rôles personnalisés pour les individus et les groupes. La capture d’écran suivante montre l’intégration Active Directory (RBAC) à l’aide du contrôle d’accès (IAM) dans le Portail Microsoft Azure :

![Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Rôles personnalisés

En plus des rôles intégrés, les utilisateurs peuvent également créer des [rôles personnalisés](../role-based-access-control/custom-roles.md) dans Azure et appliquer ces rôles aux principaux de service pour tous les abonnements dans leur locataire Active Directory. Les rôles personnalisés permettent aux utilisateurs de créer des définitions de rôles RBAC avec un ensemble personnalisé d’opérations de fournisseur de ressources. Pour savoir quelles opérations sont disponibles pour créer des rôles personnalisés pour Azure Cosmos DB veuillez consulter, [Opérations du fournisseur de ressources Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) pour les ressources Azure ?](../role-based-access-control/overview.md)
- [Rôles personnalisés pour les ressources Azure](../role-based-access-control/custom-roles.md)
- [Opérations du fournisseur de ressources Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
