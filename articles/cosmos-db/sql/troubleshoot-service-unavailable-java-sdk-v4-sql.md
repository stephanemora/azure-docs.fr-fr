---
title: Résoudre des problèmes liés aux exceptions de service non disponible Azure Cosmos DB avec le SDK Java v4
description: Découvrez comment diagnostiquer et corriger des exceptions de service non disponible Azure Cosmos DB avec le SDK Java v4.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 611870b7de3f1a60683e89653726f1453dc85cfc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116242"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnostiquer et résoudre des problèmes liés aux exceptions de service non disponible Azure Cosmos DB avec le SDK Java v4
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]
Le SDK Java v4 n’a pas pu se connecter à Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Étapes de dépannage
La liste suivante répertorie les causes connues et les solutions pour les exceptions de service non disponible.

### <a name="the-required-ports-are-being-blocked"></a>Les ports requis sont bloqués
Vérifiez que tous les [ports requis](sql-sdk-connection-modes.md#service-port-ranges) sont activés.

### <a name="client-initialization-failure"></a>Échec de l’initialisation du client
L’exception suivante est levée si le kit de développement logiciel (SDK) n’est pas en mesure de communiquer avec l’instance Cosmos DB. Celle-ci pointe normalement vers un protocole de sécurité tel qu’une règle de pare-feu bloquant les demandes.

```java
 java.lang.RuntimeException: Client initialization failed. Check if the endpoint is reachable and if your auth token is valid
```

Pour confirmer que le kit de développement logiciel (SDK) peut communiquer avec le compte Cosmos DB, exécutez la commande suivante à partir de l’emplacement d’hébergement de l’application. En cas d’échec, celle-ci pointe vers une règle de pare-feu ou une autre fonctionnalité de sécurité bloquant la demande. Si elle réussit, le kit de développement logiciel (SDK) doit être en mesure de communiquer avec le compte Cosmos DB.
```
telnet myCosmosDbAccountName.documents.azure.com 443
```

### <a name="client-side-transient-connectivity-issues"></a>Problèmes de connectivité temporaires côté client
Des exceptions non disponibles du service peuvent apparaître en cas de problèmes de connectivité temporaires qui provoquent des expirations de délai. En règle générale, l’arborescence des appels de procédure associée à ce scénario contient une erreur `ServiceUnavailableException` avec des détails du diagnostic. Par exemple :

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Suivez les [étapes de résolution des problèmes liés à l’expiration des délais de requête](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) pour la résoudre.

### <a name="service-outage"></a>Interruption de service
Vérifiez [l’état d’Azure](https://status.azure.com/status) pour voir s’il existe un problème en cours.


## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-java-sdk-v4-sql.md) des problèmes lors de l'utilisation du SDK Java v4 Azure Cosmos DB.
* Découvrez les recommandations relatives aux performances pour le [SDK Java v4](performance-tips-java-sdk-v4-sql.md).