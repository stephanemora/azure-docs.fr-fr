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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132433"
---
Les liaisons Azure Cosmos DB sont uniquement utilisables avec l’API SQL. Pour toutes les autres API Azure Cosmos DB, vous devez accéder à la base de données à partir de votre fonction en utilisant le client statique de votre API, y compris pour l'[API Azure Cosmos DB pour MongoDB](../articles/cosmos-db/mongodb-introduction.md), l'[API Cassandra](../articles/cosmos-db/cassandra-introduction.md), l'[API Gremlin](../articles/cosmos-db/graph-introduction.md), et l'[API Table](../articles/cosmos-db/table-introduction.md).
