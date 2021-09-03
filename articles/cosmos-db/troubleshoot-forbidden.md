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
ms.openlocfilehash: d1ea89443f1d4eaab359e23dfc201dc732ced75e
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108870"
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

## <a name="non-data-operations-are-not-allowed"></a>Les opérations qui ne sont pas des données ne sont pas autorisées
Ce scénario se produit lorsque des [opérations autres que des données sont interdites dans le compte](how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations). Dans ce scénario, il est courant de voir des erreurs similaires à celles ci-dessous :

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>Solution
Effectuez l’opération via Azure Resource Manager, Portail Azure, Azure CLI ou Azure PowerShell. Ou réautorisez l’exécution d’opérations non liées à des données.

## <a name="next-steps"></a>Étapes suivantes
* Configurez le [pare-feu IP](how-to-configure-firewall.md).
* Configurez l’accès à partir de [réseaux virtuels](how-to-configure-vnet-service-endpoint.md).
* Configurez l’accès depuis des [points de terminaison privés](how-to-configure-private-endpoints.md).
