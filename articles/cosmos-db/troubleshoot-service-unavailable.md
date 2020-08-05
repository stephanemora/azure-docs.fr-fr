---
title: Résoudre les problèmes liés à une exception de service non disponible Azure Cosmos DB
description: Comment diagnostiquer et corriger une exception de service non disponible Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293714"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnostiquer et résoudre les problèmes liés à un service non disponible Azure Cosmos DB
Le SDK n’a pas pu se connecter au service Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Étapes de dépannage
La liste suivante répertorie les causes connues et les solutions pour les exceptions de service non disponible.

### <a name="1-the-required-ports-are-not-enabled"></a>1. Les ports requis ne sont pas activés.
Vérifiez que tous les [ports requis](performance-tips-dotnet-sdk-v3-sql.md#networking) sont activés.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Si une application ou un service existant reçoit l’erreur 503

### <a name="1-there-is-an-outage"></a>1. Il y a une panne
Recherchez dans [État d’Azure](https://status.azure.com/status) s’il existe un problème en cours.

### <a name="2-snat-port-exhaustion"></a>2. Insuffisance de ports SNAT
Suivez le [guide sur l’insuffisance de ports SNAT](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. Les ports requis sont bloqués
Vérifiez que tous les [ports requis](performance-tips-dotnet-sdk-v3-sql.md#networking) sont activés.

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) les problèmes lors de l’utilisation du SDK .NET Azure Cosmos DB
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)