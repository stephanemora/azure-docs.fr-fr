---
title: Présence d’Azure Cosmos DB en régions
description: Cet article décrit la présence régionale d’Azure Cosmos DB et les différents environnements cloud.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: e2242e8febad5d55813721266f1286250af47111
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082883"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Présence d’Azure Cosmos DB en régions

Azure Cosmos DB est un service fondamental dans Azure et il est, par défaut, toujours disponible dans [toutes les régions où Azure est disponible](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all).

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Régions où Azure Cosmos DB est disponible" lightbox="./media/regional-presence/regional-presence.png" border="false":::

Cosmos DB est disponible dans les cinq environnements cloud Azure distincts accessibles aux clients :

* Le cloud **public Azure**, qui est disponible dans le monde entier.

* **Azure China 21Vianet** est disponible via un partenariat unique entre Microsoft et 21Vianet, l’un des fournisseurs Internet les plus importants de Chine.

* **Azure Allemagne** fournit des services sous un modèle d’administration de données, qui garantit que les données clientes sont conservées en Allemagne sous le contrôle de T-Systems International GmbH, filiale de Deutsche Telekom, agissant en tant qu’administrateur des données allemandes.

* **Azure Government** est disponible dans quatre régions des États-Unis pour les organismes publics américains et leurs partenaires. 

* **Azure Government for Department of Defense (DoD)** est disponible dans deux régions des États-Unis pour le département de la Défense des États-Unis.

## <a name="regional-presence-with-global-distribution"></a>Présence régionale avec distribution mondiale

Toutes les API exposées par Azure Cosmos DB (notamment SQL, MongoDB, Cassandra, Gremlin et Table) sont disponibles dans toutes les régions Azure par défaut. Par exemple, vous pouvez avoir des API MongoDB et Cassandra exposées par Azure Cosmos DB non seulement dans toutes les régions Azure du monde, mais également dans des régions cloud souveraines telles que les régions Chine, Allemagne, Government et Department of Defense (DoD).

Azure Cosmos DB est un service de base de données [mondialement distribué](distribute-data-globally.md). Vous pouvez associer n’importe quel nombre de régions Azure avec votre compte Azure Cosmos, et vos données sont répliquées de manière automatique et transparente. Vous pouvez ajouter ou supprimer une région à votre compte Azure Cosmos à tout moment. Avec la fonctionnalité de distribution mondiale et le protocole de réplication multi-maître, Azure Cosmos DB offre une latence de lecture et d’écriture inférieure à 10 ms au 99e centile, une disponibilité de lecture et d’écriture de 99,999 %, et la possibilité d’effectuer une mise à l’échelle élastique du débit provisionné pour les lectures et écritures dans toutes les régions associées à votre compte Azure Cosmos. Azure Cosmos DB offre également cinq modèles de cohérence bien définis, et vous pouvez choisir d’appliquer un modèle de cohérence spécifique à vos données. Pour finir, Azure Cosmos DB est le seul service de base de données qui fournit un [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) complet englobant le débit provisionné, la latence au 99e centile, la haute disponibilité et la cohérence. Les fonctionnalités ci-dessus sont disponibles dans tous les clouds Azure.

La prise en charge de la réplication à l’échelle mondiale est limitée à un environnement cloud Azure. Par exemple, les comptes Azure Cosmos DB sous le cloud *Public Azure* peuvent être répliqués vers n’importe quelle région Azure au sein du cloud *Public* Azure, mais ils ne peuvent pas être répliqués vers des régions Azure du cloud *Azure China 21Vianet*.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant découvrir les différents concepts fondamentaux d’Azure Cosmos DB avec les articles suivants :

* [Distribution mondiale des données](distribute-data-globally.md)
* [Guide pratique pour gérer un compte Azure Cosmos DB](manage-account.md)
* [Provisionner le débit pour les conteneurs et bases de données Azure Cosmos](set-throughput.md)
* [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
