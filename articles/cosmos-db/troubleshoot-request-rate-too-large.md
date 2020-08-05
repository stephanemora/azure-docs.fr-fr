---
title: Résoudre les problèmes liés à l’exception Taux de requêtes trop élevé Azure Cosmos DB
description: Comment diagnostiquer et corriger l’exception Taux de requêtes trop élevé
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293715"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Diagnostiquer et résoudre les problèmes liés à un trop grand nombre de requêtes Cosmos DB
« Taux de requêtes trop élevé » ou le code d’erreur 429 indique que vos requêtes sont limitées.

## <a name="troubleshooting-steps"></a>Étapes de dépannage
La liste suivante répertorie les causes connues et les solutions pour un trop grand nombre de requêtes.

### <a name="1-check-the-metrics"></a>1. Vérifier les métriques
Le client doit examiner les [données de supervision Azure Cosmos DB](monitor-cosmos-db.md) pour vérifier la présence d’exceptions numéro 429.

## <a name="cause"></a>Cause :
Le débit consommé (RU/s) a dépassé le [débit provisionné](set-throughput.md). Le SDK envoie automatiquement de nouvelles demandes en fonction de la stratégie de nouvelles tentatives en place. Si cet échec se produit souvent, envisagez d’augmenter le débit de la collection. Examinez les métriques du portail pour voir si vous obtenez des erreurs 429. Examinez votre clé de partition pour vous assurer qu’elle entraîne une [répartition uniforme du stockage et du volume de demandes](partition-data.md).

## <a name="solution"></a>Solution :
1. Utilisez le [portail ou SDK](set-throughput.md) pour augmenter le débit provisionné.
2. Basculez la base de données ou le conteneur vers la [mise à l’échelle automatique](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) les problèmes lors de l’utilisation du SDK .NET Azure Cosmos DB
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)