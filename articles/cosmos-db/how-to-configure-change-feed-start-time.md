---
title: Configurer l’heure de début du processeur de flux de modification - Azure Cosmos DB
description: Découvrez comment configurer le processeur de flux de modification pour qu’il commence la lecture depuis une date et une heure spécifiques
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: d3d9af1b8cbda3f0fa2ff4650fef4602f7812f79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261713"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Configurer l’heure de début du processeur de flux de modification

Cet article décrit comment vous pouvez configurer le [processeur de flux de modification](./change-feed-processor.md) pour qu’il commence à lire à partir d’une date et d’une heure spécifiques.

## <a name="default-behavior"></a>Comportement par défaut

Lorsqu’un processeur de flux de modification démarre la première fois, il initialise le conteneur de baux et démarre son [cycle de vie de traitement](./change-feed-processor.md#processing-life-cycle). Toutes les modifications survenues dans le conteneur avant la première initialisation du processeur de flux de modification ne seront pas détectées.

## <a name="reading-from-a-previous-date-and-time"></a>Lecture à partir d’une date et d’une heure précédentes

Il est possible d’initialiser le processeur de flux de modification pour lire les modifications à partir d’une **date et d’une heure spécifiques**, en passant une instance `DateTime` dans l’extension de générateur `WithStartTime` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Le processeur de flux de modification sera initialisé pour cette date et cette heure spécifiques et commencera à lire les modifications survenues en amont.

## <a name="reading-from-the-beginning"></a>Lecture à partir du début

Dans d’autres scénarios tels que la migration de données ou l’analyse de l’intégralité de l’historique d’un conteneur, nous devons lire le flux de modification à partir du **début de la durée de vie de ce conteneur**. Pour ce faire, nous pouvons utiliser `WithStartTime` sur l’extension du générateur, mais en passant `DateTime.MinValue.ToUniversalTime()`, ce qui génère la représentation UTC de la valeur `DateTime` minimale, comme ci-après :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Le processeur de flux de modification est initialisé et commence à lire les modifications à partir du début de la durée de vie du conteneur.

> [!NOTE]
> Ces options de personnalisation ne fonctionnent que pour configurer le point de départ dans le temps du processeur de flux de modification. Une fois que le conteneur de baux a été initialisé pour la première fois, les modifier n’a plus aucun effet.

> [!NOTE]
> Lorsque vous spécifiez un point dans le temps, seules les modifications apportées aux éléments qui existent actuellement dans le conteneur sont lues. Si un élément a été supprimé, son historique sur le flux de modification est également supprimé.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Exemples d’utilisation sur GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemples supplémentaires sur GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le processeur de flux de modification, consultez les articles suivants :

* [Vue d’ensemble du processeur de flux de modification](change-feed-processor.md)
* [Utilisation de l’estimateur de flux de modification](how-to-use-change-feed-estimator.md)
