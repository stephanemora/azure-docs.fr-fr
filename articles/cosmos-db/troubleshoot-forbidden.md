---
title: Résoudre les problèmes liés à des exceptions interdites Azure Cosmos DB
description: Découvrez comment diagnostiquer et résoudre des exceptions interdites.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99971889"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostiquer et résoudre les problèmes liés à des exceptions interdites Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le code d’état HTTP 403 indique que la demande n’est pas autorisée à se terminer.

## <a name="firewall-blocking-requests"></a>Requêtes de blocage de pare-feu
Dans ce scénario, il est courant de voir des erreurs similaires à celles ci-dessous :

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Solution
Vérifiez que les [paramètres de votre pare-feu](how-to-configure-firewall.md) sont corrects, puis incluez les adresses IP ou les réseaux à partir desquels vous essayez de vous connecter.
Si vous les avez récemment mis à jour, gardez à l’esprit que les modifications peuvent prendre **jusqu’à 15 minutes pour être appliquées**.

## <a name="next-steps"></a>Étapes suivantes
* Configurez le [pare-feu IP](how-to-configure-firewall.md).
* Configurez l’accès à partir de [réseaux virtuels](how-to-configure-vnet-service-endpoint.md).
* Configurez l’accès depuis des [points de terminaison privés](how-to-configure-private-endpoints.md).
