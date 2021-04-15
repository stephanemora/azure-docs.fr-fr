---
title: Fichier include
description: Fichier include
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 698219a446240950b9a661f923109fbb2f863556
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178404"
---
Si vous utilisez actuellement le stockage de table Azure, vous bénéficiez des avantages suivants en passant à l’API Table d’Azure Cosmos DB :

|Fonctionnalité | Stockage de tables Azure | API Table d’Azure Cosmos DB |
| --- | --- | --- |
| Latence | Rapide, mais aucune limite supérieure sur la latence. | Une latence de quelques millisecondes pour les lectures et écritures, appuyée par des lectures avec une latence de < 10 ms et des écritures avec une latence de < 15 ms au 99e centile, à n’importe quelle échelle, n’importe où dans le monde. |
| Débit | Modèle de débit variable. Les tables ont une limite d’évolutivité de 20 000 opérations/s. | Hautement évolutif avec un [débit dédié réservé par table](../articles/cosmos-db/request-units.md), qui est appuyé par des contrats de niveau de service. Les comptes n’ont pas de limite maximale concernant le débit, et prennent en charge plus de 10 millions d’opérations/s par table (en mode de débit provisionné). |
| Diffusion mondiale | Une région unique avec région de lecture secondaire accessible en lecture à des fins de haute disponibilité, prenant en charge le basculement automatique et manuel des comptes. | [Une distribution mondiale clé en main](../articles/cosmos-db/distribute-data-globally.md) de 1 à plus de 30 régions. Prise en charge des [basculements automatiques et manuels](../articles/cosmos-db/high-availability.md) à tout moment, partout dans le monde. |
| Indexation | Index primaire uniquement sur PartitionKey et RowKey. Pas d’index secondaire. | Indexation automatique et complète de toutes les propriétés, aucune gestion des index. |
| Requête | L’exécution des requêtes utilise un index de clé primaire, et effectue une recherche dans le cas contraire. | Les requêtes peuvent tirer parti de l’indexation automatique de propriétés pour des temps de requête rapides. |
| Cohérence | Forte au sein de la région primaire. Éventuelle au sein de la région secondaire. | [Cinq niveaux de cohérence bien définis](../articles/cosmos-db/consistency-levels.md) pour compenser la disponibilité, la latence, le débit ou la cohérence en fonction des besoins de votre application. |
| Tarifs | Basés sur la consommation. | Disponibles à la fois en mode [basé sur la consommation](../articles/cosmos-db/serverless.md) et en mode [capacité provisionnée](../articles/cosmos-db/set-throughput.md). |
| Contrats SLA | Disponibilité de 99,99 %. | Un contrat SLA avec une disponibilité à 99,99 % pour tous les comptes à région unique et à plusieurs régions avec cohérence souple, ainsi qu’une disponibilité de lecture à 99,999 % pour tous les comptes de base de données à plusieurs régions [Contrats SLA complets à la pointe du secteur](https://azure.microsoft.com/support/legal/sla/cosmos-db/) sur la disponibilité générale. |
