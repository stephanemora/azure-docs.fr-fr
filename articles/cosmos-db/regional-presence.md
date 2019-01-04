---
title: Présence d’Azure Cosmos DB en régions
description: Cet article décrit la présence régionale d’Azure Cosmos DB et les différents environnements cloud.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: c3299b2ea777577281ede234cd1aa5137ce5a445
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138107"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Présence d’Azure Cosmos DB dans les régions

Actuellement, Azure est disponible dans [54 régions](https://azure.microsoft.com/global-infrastructure/regions/) dans le monde. Azure Cosmos DB est un service fondamental dans Azure et est disponible dans toutes les régions où Azure est disponible.

![Disponibilité d’Azure Cosmos DB dans les régions](./media/regional-presence/regional-presence.png)

Cosmos DB est disponible dans les cinq environnements cloud Azure distincts accessibles aux clients :

* Le cloud **public Azure**, qui est disponible dans le monde entier.

* **Azure - Chine** est disponible via un partenariat unique entre Microsoft et 21Vianet, l’un des fournisseurs Internet les plus importants du pays.

* **Azure - Allemagne** fournit des services sous un modèle d’administration de données, qui garantit que les données clientes sont conservées en Allemagne sous le contrôle de T-Systems International GmbH, société Deutsche Telecom responsable de l’administration des données allemandes.

* **Azure Government** est disponible dans quatre régions des États-Unis pour les organismes publics américains et leurs partenaires. 

* **Azure Government pour le département de la Défense** est disponible dans deux régions des États-Unis pour le département de la Défense des États-Unis.

## <a name="regional-presence-with-global-distribution"></a>Présence régionale avec distribution mondiale

Différentes API exposées par Azure Cosmos DB (notamment SQL, MongoDB, Cassandra, Gremlin et le stockage Table Azure) sont disponibles dans toutes les régions Azure. Par exemple, vous pouvez avoir des API MongoDB et Cassandra exposées par Azure Cosmos DB non seulement dans toutes les régions Azure du monde, mais également dans des régions souveraines Azure telles que les régions Chine, Allemagne, Government et Department of Defense (DoD).

Azure Cosmos DB est une base de données [mondialement distribuée](distribute-data-globally.md). Vous pouvez associer n’importe quel nombre de régions Azure avec votre compte Azure Cosmos, et vos données sont répliquées de manière automatique et transparente. Vous pouvez ajouter ou supprimer une région à votre compte Azure Cosmos à tout moment. Avec la fonctionnalité de distribution mondiale et le protocole de réplication multi-maître, Azure Cosmos DB offre une latence de lecture et d’écriture inférieure à 10 ms au 99e centile, une disponibilité de lecture et d’écriture de 99,999 %, et la possibilité d’effectuer une mise à l’échelle élastique du débit provisionné pour les lectures et écritures dans toutes les régions associées à votre compte Azure Cosmos. Azure Cosmos DB offre également cinq modèles de cohérence bien définis, et vous pouvez choisir d’appliquer un modèle de cohérence spécifique à vos données. Pour finir, Azure Cosmos DB est le seul service de base de données qui fournit un contrat de niveau de service (SLA) complet englobant le débit provisionné, la latence au 99e centile, la haute disponibilité et la cohérence.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant découvrir les différents concepts d’Azure Cosmos DB avec les articles suivants :

* [Distribution mondiale des données](distribute-data-globally.md)
* [Guide pratique pour gérer un compte Azure Cosmos DB](manage-account.md)
* [Provisionner le débit pour les conteneurs et bases de données Azure Cosmos](set-throughput.md)
* [Contrat SLA Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
