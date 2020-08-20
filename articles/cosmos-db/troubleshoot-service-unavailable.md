---
title: Résoudre les problèmes liés à une exception de service non disponible Azure Cosmos DB
description: Comment diagnostiquer et corriger une exception de service non disponible Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987373"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnostiquer et résoudre les problèmes liés à un service non disponible Azure Cosmos DB
Le SDK n’a pas pu se connecter au service Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Étapes de dépannage
La liste suivante répertorie les causes connues et les solutions pour les exceptions de service non disponible.

### <a name="1-the-required-ports-are-being-blocked"></a>1. Les ports requis sont bloqués
Vérifiez que tous les [ports requis](performance-tips-dotnet-sdk-v3-sql.md#networking) sont activés.

### <a name="2-client-side-transient-connectivity-issues"></a>2. Problèmes de connectivité temporaires côté client
Des exceptions d’indisponibilité du service peuvent apparaître en cas de problèmes de connectivité temporaires qui provoquent des expirations de délai. En règle générale, l’arborescence des appels de procédure associée à ce scénario contient un `TransportException`, par exemple :

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Suivez la [résolution des problèmes liés à l’expiration des délais de requête](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) pour les résoudre.

### <a name="3-service-outage"></a>3. Interruption de service
Recherchez dans [État d’Azure](https://status.azure.com/status) s’il existe un problème en cours.


## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) les problèmes lors de l’utilisation du SDK .NET Azure Cosmos DB
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)