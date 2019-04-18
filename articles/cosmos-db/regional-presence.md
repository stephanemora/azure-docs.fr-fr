---
title: Présence d’Azure Cosmos DB en régions
description: Cet article décrit la présence régionale d’Azure Cosmos DB et les différents environnements cloud.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 851da72a308b164b9a01b7ccfa3d541fc06810ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762189"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Présence d’Azure Cosmos DB en régions

Azure Cosmos DB est un service de base dans Azure et, par défaut, est toujours disponible dans toutes les régions, où Azure est disponible. Actuellement, Azure est disponible dans [54 régions](https://azure.microsoft.com/global-infrastructure/regions/) dans le monde. 

[![Régions où Azure Cosmos DB est disponible](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB est disponible dans les cinq environnements cloud Azure distincts accessibles aux clients :

* Le cloud **public Azure**, qui est disponible dans le monde entier.

* **Azure China 21Vianet** est disponible via un partenariat unique entre Microsoft et 21Vianet, un des plus grands fournisseurs d’internet du pays en Chine.

* **Azure - Allemagne** fournit des services sous un modèle d’administration de données, qui garantit que les données clientes sont conservées en Allemagne sous le contrôle de T-Systems International GmbH, société Deutsche Telecom responsable de l’administration des données allemandes.

* **Azure Government** est disponible dans quatre régions des États-Unis pour les organismes publics américains et leurs partenaires. 

* **Azure Government pour le ministère de la défense (DoD)** est disponible dans deux régions aux États-Unis pour le ministère américain de la défense.

## <a name="regional-presence-with-global-distribution"></a>Présence régionale avec distribution mondiale

Toutes les API exposées par Azure Cosmos DB (y compris SQL, MongoDB, Cassandra, Gremlin et Table) sont disponibles dans toutes les régions Azure par défaut. Par exemple, vous pouvez avoir MongoDB et Cassandra API exposées par Azure Cosmos DB dans toutes les régions Azure globales, mais également dans les clouds souverains comme en Chine, Allemagne, gouvernement et Department of Defense régions (DoD).

Azure Cosmos DB est un [globalement distribuées](distribute-data-globally.md) service de base de données. Vous pouvez associer n’importe quel nombre de régions Azure avec votre compte Azure Cosmos, et vos données sont répliquées de manière automatique et transparente. Vous pouvez ajouter ou supprimer une région à votre compte Azure Cosmos à tout moment. Avec la fonctionnalité de distribution mondiale et le protocole de réplication multi-maître, Azure Cosmos DB offre une latence de lecture et d’écriture inférieure à 10 ms au 99e centile, une disponibilité de lecture et d’écriture de 99,999 %, et la possibilité d’effectuer une mise à l’échelle élastique du débit provisionné pour les lectures et écritures dans toutes les régions associées à votre compte Azure Cosmos. Azure Cosmos DB offre également cinq modèles de cohérence bien définis, et vous pouvez choisir d’appliquer un modèle de cohérence spécifique à vos données. Enfin, Azure Cosmos DB est le seul service de base de données dans le secteur qui fournit une gamme complète [contrat de niveau de Service (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) débit approvisionné englobant, une latence au 99e centile, la haute disponibilité, et cohérence. Les fonctionnalités ci-dessus sont disponibles dans tous les clouds Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant découvrir les concepts fondamentaux d’Azure Cosmos DB avec les articles suivants :

* [Distribution mondiale des données](distribute-data-globally.md)
* [Guide pratique pour gérer un compte Azure Cosmos DB](manage-account.md)
* [Provisionner le débit pour les conteneurs et bases de données Azure Cosmos](set-throughput.md)
* [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
