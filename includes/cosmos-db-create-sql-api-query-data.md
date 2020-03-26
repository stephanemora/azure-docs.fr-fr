---
title: Fichier Include
description: Fichier Include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 64c900e01496dad0d5f6a96d8d790ae0c2a8c95a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78927364"
---
Vous pouvez utiliser des requêtes dans l’Explorateur de données pour récupérer et filtrer vos données.

1. Dans l’Explorateur de données, en haut de l’onglet **Éléments**, examinez la requête par défaut `SELECT * FROM c`. Cette requête par défaut récupère et affiche tous les documents de la collection, classés par ID. 
   
   ![La requête par défaut dans l’Explorateur de données est « SELECT * FROM c »](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Pour modifier la requête, sélectionnez **Modifier le filtre**, remplacez la requête par défaut par `ORDER BY c._ts DESC`, puis sélectionnez **Appliquer le filtre**.
   
   ![Modifier la requête par défaut en ajoutant ORDER BY c._ts DESC et en cliquant sur Appliquer un filtre](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Cette requête modifiée affiche les documents dans l’ordre décroissant en fonction de leur horodatage. Votre deuxième document s’affiche désormais en tête de liste. 
   
   ![Requête remplacée par ORDER BY c._ts DESC et sélection de l’option Appliquer le filtre](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Si vous êtes à l’aise avec la syntaxe SQL, vous pouvez saisir n’importe quelle [requête SQL](../articles/cosmos-db/sql-api-sql-query.md) prise en charge dans la zone du prédicat de requête. Vous pouvez également utiliser l’Explorateur de données pour créer des procédures stockées, des fonctions définies par l’utilisateur et des déclencheurs pour la logique métier côté serveur. 

L’Explorateur de données permet d’accéder facilement à toutes les fonctionnalités intégrées d’accès aux données par programmation qui sont disponibles dans les API, à partir du portail Azure. Vous pouvez également utiliser le portail pour mettre à l’échelle le débit, pour obtenir des clés et des chaînes de connexion, ainsi que pour passer en revue les métriques et les contrats SLA de votre compte Azure Cosmos DB. 

