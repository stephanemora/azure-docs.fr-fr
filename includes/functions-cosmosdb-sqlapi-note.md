---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: bb915c18965a55c2b1fc7fe3cf1fa923d7f43fd6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052517"
---
Les liaisons Azure Cosmos DB sont uniquement utilisables avec l’API SQL. Pour toutes les autres API Azure Cosmos DB, vous devez accéder à la base de données à partir de votre fonction en utilisant le client statique pour votre API, incluant [l’API MongoDB](../articles/cosmos-db/mongodb-introduction.md), [l’API Cassandra](../articles/cosmos-db/cassandra-introduction.md), [l’API Gremlin](../articles/cosmos-db/graph-introduction.md) et [l’API Table](../articles/cosmos-db/table-introduction.md).
