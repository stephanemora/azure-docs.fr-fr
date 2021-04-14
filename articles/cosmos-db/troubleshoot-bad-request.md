---
title: Résoudre les problèmes d’exceptions liées à des demandes incorrectes Azure Cosmos DB
description: Découvrez comment diagnostiquer et corriger les exceptions en lien avec des demandes incorrectes telles que le contenu d’entrée ou la clé de partition n’est pas valide ou la clé de partition ne correspond pas dans Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555838"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Diagnostiquer et résoudre les exceptions en lien avec des demandes incorrectes dans Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le code d’état HTTP 400 représente le fait que la demande contient des données non valides ou que des paramètres obligatoires sont manquants.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>Propriété ID manquante
Dans ce scénario, il est courant de voir l’erreur suivante :

*Le contenu d’entrée n’est pas valide, car les propriétés obligatoires (ID) sont absentes*

Une réponse avec cette erreur signifie que le document JSON qui est envoyé au service n’a pas la propriété ID requise.

### <a name="solution"></a>Solution
Spécifiez une propriété `id` avec une valeur de chaîne telle qu’indiquée dans la [spécification REST](/rest/api/cosmos-db/documents) au sein de votre document. Les SDK ne génèrent pas automatiquement de valeurs pour cette propriété.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Type de clé de partition non valide
Dans ce scénario, il est courant de voir des erreurs semblables à celle-ci :

*La clé de partition... n’est pas valide.*

Une réponse avec cette erreur signifie que la valeur de la clé de partition est d’un type non valide.

### <a name="solution"></a>Solution
La valeur de la clé de partition doit être une chaîne ou un nombre. Veillez à ce que la valeur soit du type attendu.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Valeur de clé de partition erronée
Dans ce scénario, il est courant de voir l’erreur suivante :

*Le PartitionKey extrait du document ne correspond pas à celui spécifié dans l’en-tête*

Une réponse avec cette erreur signifie que vous exécutez une opération et transmettez une valeur de clé de partition qui ne correspond pas à la valeur de corps du document pour la propriété attendue. Si le chemin de la clé de partition de la collection est `/myPartitionKey`, le document a une propriété appelée `myPartitionKey` avec une valeur qui ne correspond pas à ce qui a été fourni comme valeur de clé de partition lors de l’appel de la méthode SDK.

### <a name="solution"></a>Solution
Envoyez le paramètre de valeur de clé de partition qui correspond à la valeur de propriété de document.

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) des problèmes lors de l’utilisation du kit de développement logiciel (SDK) .NET Azure Cosmos DB.
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md).
* [Diagnostiquer et résoudre](troubleshoot-java-sdk-v4-sql.md) des problèmes lors de l'utilisation du SDK Java v4 Azure Cosmos DB.
* Découvrez les recommandations relatives aux performances pour le [SDK Java v4](performance-tips-java-sdk-v4-sql.md).
