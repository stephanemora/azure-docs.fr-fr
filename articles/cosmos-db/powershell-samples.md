---
title: Exemples Azure PowerShell pour Azure Cosmos DB
description: Exemples Azure PowerShell - Des scripts pour vous aider à créer et gérer des comptes Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069299"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemples Azure PowerShell pour Azure Cosmos DB

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour l’API Azure Cosmos DB pour Core (SQL).

| |  |
|---|---|
|**Comptes Azure Cosmos**||
|[Créer un compte](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte d’API SQL Azure Cosmos. |
|[Obtenir un compte](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenir les propriétés d’un compte Azure Cosmos. |
|[Ajouter une région](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenir un compte Azure Cosmos et ajouter une région à la liste des emplacements. |
|[Modifier la priorité de basculement](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Modifier la priorité de basculement d’un compte Azure Cosmos avec un déclencheur de basculement manuel. |
|[Mettre à jour les balises](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettre à jour les balises pour un compte Azure Cosmos. |
|[Obtenir les clés de compte](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenir les clés primaires et secondaires d’un compte Azure Cosmos. |
|[Régénération des clés de compte](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Régénérer les clés primaires et secondaires d’un compte Azure Cosmos. |
|[Répertorier les chaînes de connexion](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenir les chaînes de connexion primaires et secondaires d’un compte Azure Cosmos. |
|[Créer un pare-feu IP](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créer un pare-feu IP pour un compte Azure Cosmos. |
|[Supprimer un compte Azure Cosmos](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Supprimer un compte Azure Cosmos. |
|**Bases de données Azure Cosmos**||
| [Créer une base de données](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer une base de données au sein d’un compte Azure Cosmos.|
| [Créer une base de données avec un débit partagé/au niveau de la base de données](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer une base de données Azure Cosmos avec un débit au niveau de la base de données qui est partagé avec ses conteneurs.|
| [Répertorier toutes les bases de données](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Répertorier toutes les bases de données d’un compte Azure Cosmos.|
| [Obtenir une base de données](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtenir les propriétés d’une base de données Azure Cosmos.|
|**Conteneurs Cosmos Azure**||
| [Créer un conteneur](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos avec un débit dédié.|
| [Créer un conteneur avec un débit partagé](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos dont le débit est partagé avec d’autres conteneurs de la base de données.|
| [Créer un conteneur avec stratégie d’index](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos avec une stratégie d’index personnalisée.|
| [Créer un conteneur sans stratégie d’index](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos dont la stratégie d’index est désactivée.|
| [Créer un conteneur à clés et à durée de vie uniques](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos avec une contrainte de clé et une durée de vie uniques configurées.|
| [Créer un conteneur avec résolution des conflits](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos avec une stratégie de résolution des conflits de type last-writer-wins (dernière version valide).|
| [Répertorier tous les conteneurs](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Répertorier tous les conteneurs dans une base de données Azure Cosmos.|
| [Obtenir un conteneur](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtenir les propriétés d’un conteneur dans une base de données Azure Cosmos.|
| [Supprimer un conteneur](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Supprimer un conteneur dans une base de données Azure Cosmos.|
|||