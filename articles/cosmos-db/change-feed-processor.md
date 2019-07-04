---
title: Utilisation de la bibliothèque du processeur de flux de modification dans Azure Cosmos DB
description: Utilisation de la bibliothèque du processeur de flux de modification Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: d0faeba5278e23990a72c9d2dd3d7e18510bdf80
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342055"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processeur de flux de modification dans Azure Cosmos DB 

La [bibliothèque du processeur de flux de modification Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md) vous permet de répartir le traitement des événements sur plusieurs consommateurs. Cette bibliothèque simplifie la lecture des modifications sur plusieurs partitions et threads exécutés en parallèle.

Le principal avantage de la bibliothèque du processeur de flux de modification est que vous n’êtes pas obligé de gérer chaque partition et jeton de liaison, et que vous n’avez pas à interroger chaque conteneur manuellement.

La bibliothèque du processeur de flux de modification simplifie la lecture des modifications sur plusieurs partitions et threads exécutés en parallèle. Elle gère automatiquement la lecture des modifications sur les différentes partitions à l’aide d’un mécanisme de bail. Comme vous pouvez le voir dans l’image suivante, si vous démarrez deux clients qui utilisent la bibliothèque du processeur de flux de modification, ces deux clients se partagent la charge de travail. Plus vous ajoutez de clients, plus la charge de travail est répartie entre ces clients.

![Utilisation de la bibliothèque du processeur de flux de modification Azure Cosmos DB](./media/change-feed-processor/change-feed-output.png)

Le client de gauche a été démarré en premier et a commencé à analyser l’ensemble des partitions. Puis, lors du démarrage du deuxième client, le premier client lui a transmis certains des baux. Cela permet de distribuer facilement la charge de travail entre différents ordinateurs et clients.

Si vous avez deux fonctions Azure serverless qui analysent le même conteneur et utilisent le même bail, les deux fonctions peuvent obtenir des documents différents en fonction de la manière dont la bibliothèque du processeur décide de traiter les partitions.

## <a name="implementing-the-change-feed-processor-library"></a>Implémentation de la bibliothèque du processeur de flux de modification

Il existe quatre composants principaux dans l’implémentation de la bibliothèque du processeur de flux de modification : 

1. **Conteneur supervisé :** le conteneur supervisé est constitué des données à partir desquelles le flux de modification est généré. Toutes les insertions et les modifications apportées au conteneur supervisé sont répercutées dans le flux de modification du conteneur.

1. **Conteneur de baux :** le conteneur de baux coordonne le traitement du flux de modification entre les threads de travail (workers). Un conteneur distinct est utilisé pour stocker les baux avec un bail par partition. Il est préférable de stocker ce conteneur de baux sur un autre compte, avec une zone d’écriture plus proche de l’endroit où le processeur de flux de modification s’exécute. Un objet de bail contient les attributs suivants :

   * Propriétaire : spécifie l’hôte détenteur du bail.

   * Continuation : spécifie la position (jeton de continuation) d’une partition particulière dans le flux de modification.

   * Timestamp : heure de dernière mise à jour du bail. Vous pouvez utiliser l’horodatage pour vérifier si le bail est considéré expiré.

1. **Hôte de processeur :** chaque hôte détermine le nombre de partitions à traiter en fonction du nombre d’instances d’hôtes restantes qui disposent de baux actifs.

   * Quand un hôte démarre, il acquiert des baux pour équilibrer la charge de travail parmi tous les hôtes. Un hôte renouvelle périodiquement les baux afin que ceux-ci restent actifs.

   * Un hôte crée un point de contrôle pour le dernier jeton de continuation de son bail pour chaque lecture. Pour garantir la sécurité de l’accès concurrentiel, un hôte vérifie l’ETag pour chaque mise à jour de bail. D’autres stratégies de point de contrôle sont également prises en charge.

   * En cas d’arrêt, un hôte libère tous les baux mais conserve les informations de continuation, afin de pouvoir reprendre ultérieurement la lecture à partir du point de contrôle stocké.

   Actuellement, le nombre d’hôtes ne peut pas être supérieur au nombre de partitions (baux).

1. **Consommateurs :** les consommateurs, ou workers, sont des threads qui exécutent le traitement du flux de modification initié par chaque hôte. Chaque hôte de processeur peut avoir plusieurs consommateurs. Chaque consommateur lit le flux de modification à partir de la partition à laquelle il est assigné, et il informe son hôte des modifications et des baux expirés.

Pour mieux comprendre comment ces quatre éléments du processeur de flux de modification interagissent, examinons un exemple à l’aide du schéma suivant. La collection analysée stocke des documents et utilise « city » comme clé de partition. Nous constatons que la partition bleue contient des documents avec le champ « city » de « A-E », et ainsi de suite. Il existe deux hôtes, chacun avec deux consommateurs lisant à partir des quatre partitions en parallèle. Les flèches indiquent les consommateurs lisant à partir d’un point spécifique dans le flux de modification. Dans la première partition, le bleu foncé représente les modifications non lues, tandis que le bleu clair représente les modifications déjà lues sur le flux de modification. Les hôtes utilisent la collection de baux pour stocker une valeur de « continuation » afin d’assurer le suivi de la position de lecture actuelle pour chaque consommateur.

![Exemple de processeur de flux de modification](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Flux de modification et débit provisionné

Vous êtes facturé pour les unités de requête consommées, car des unités de requête sont consommées lorsque vous déplacez des données vers et à partir des conteneurs Cosmos. La facturation tient compte de toutes les unités de requête consommées par le conteneur de baux.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Bibliothèque du processeur de flux de modification Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md)
* [Package NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exemples supplémentaires sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de modification, consultez les articles suivants :

* [Présentation du flux de modification](change-feed.md)
* [Manières de lire le flux de modification](read-change-feed.md)
* [Utilisation du flux de modification avec Azure Functions](change-feed-functions.md)
