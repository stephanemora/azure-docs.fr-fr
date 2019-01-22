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
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360171"
---
Les liaisons Azure Cosmos DB sont uniquement utilisables avec l’API SQL. Pour toutes les autres API Azure Cosmos DB, vous devez accéder à la base de données à partir de votre fonction en utilisant le client statique de votre API, y compris pour l'[API Azure Cosmos DB pour MongoDB](../articles/cosmos-db/mongodb-introduction.md), l'[API Cassandra](../articles/cosmos-db/cassandra-introduction.md), l'[API Gremlin](../articles/cosmos-db/graph-introduction.md), et l'[API Table](../articles/cosmos-db/table-introduction.md).
