---
title: Résoudre les problèmes liés à une exception d’expiration du délai de demande de service Azure Cosmos DB
description: Comment diagnostiquer et corriger une exception d’expiration du délai de demande de service Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293716"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Diagnostiquer et résoudre les problèmes liés à l’expiration du délai de demande Azure Cosmos DB
Azure Cosmos DB a retourné une exception HTTP 408 Expiration du délai de la demande

## <a name="troubleshooting-steps"></a>Étapes de dépannage
La liste suivante répertorie les causes connues et les solutions pour les exceptions de dépassement de délai de demande.

### <a name="1-check-the-sla"></a>1. Vérification du contrat de niveau de service
Le client doit examiner les [données de supervision Azure Cosmos DB](monitor-cosmos-db.md) pour vérifier si le nombre d’exceptions 408 enfreint le contrat de niveau de service Cosmos DB.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Solution 1 : Il n’a pas enfreint le contrat de niveau de service Cosmos DB
L’application doit gérer ce scénario et réessayer sur ces échecs temporaires.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Solution 2 : Il a enfreint le contrat de niveau de service Cosmos DB
Contactez le support Azure : https://aka.ms/azure-support.
 
### <a name="2-hot-partition-key"></a>2. Clé de partition à chaud
Azure Cosmos DB répartit le débit global approvisionné de manière uniforme entre les partitions physiques. Lorsqu’il existe une partition à chaud, une ou plusieurs clés de partition logique sur une partition physique consomment la totalité des RU/s de la partition physique, tandis que les RU/s sur d’autres partitions physiques ne sont pas utilisées. Par conséquent, le nombre total de RU/s consommées sera inférieur au nombre global de RU/s provisionnées dans la base de données ou le conteneur, mais vous verrez toujours une limitation (429s) sur les demandes par rapport à la clé de partition logique à chaud. Utilisez la [métrique Consommation d’unités de requête normalisée](monitor-normalized-request-units.md) pour voir si la charge de travail rencontre une partition à chaud. 

#### <a name="solution"></a>Solution :
Choisissez une clé de partition appropriée qui répartit uniformément le volume de demandes et le stockage. Découvrez comment [changer votre clé de partition](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) les problèmes lors de l’utilisation du SDK .NET Azure Cosmos DB
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)