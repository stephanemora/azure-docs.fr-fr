---
title: Fichier include
description: Fichier include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85115203"
---
Vous pouvez utiliser des requêtes dans l’Explorateur de données pour récupérer et filtrer vos données.

1. Dans l’Explorateur de données, en haut de l’onglet **Éléments**, examinez la requête par défaut `SELECT * FROM c`. Cette requête récupère et affiche tous les documents du conteneur, classés par ID. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="La requête par défaut dans l’Explorateur de données est SELECT * FROM c":::
   
1. Pour modifier la requête, sélectionnez **Modifier le filtre**, remplacez la requête par défaut par `ORDER BY c._ts DESC`, puis sélectionnez **Appliquer le filtre**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="La requête par défaut dans l’Explorateur de données est SELECT * FROM c":::

   Cette requête modifiée affiche les documents dans l’ordre décroissant en fonction de leur horodatage. Votre deuxième document s’affiche désormais en tête de liste. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="La requête par défaut dans l’Explorateur de données est SELECT * FROM c":::

Si vous êtes à l’aise avec la syntaxe SQL, vous pouvez saisir n’importe quelle [requête SQL](../articles/cosmos-db/sql-api-sql-query.md) prise en charge dans la zone du prédicat de requête. Vous pouvez également utiliser l’Explorateur de données pour créer des procédures stockées, des fonctions définies par l’utilisateur et des déclencheurs pour la logique métier côté serveur. 

L’Explorateur de données permet d’accéder facilement à toutes les fonctionnalités intégrées d’accès aux données par programmation qui sont disponibles dans les API, à partir du portail Azure. Vous pouvez également utiliser le portail pour mettre à l’échelle le débit, pour obtenir des clés et des chaînes de connexion, ainsi que pour passer en revue les métriques et les contrats SLA de votre compte Azure Cosmos DB. 

