---
title: Arrêter/Démarrer - Portail Azure - Serveur flexible Azure Database pour PostgreSQL
description: Cet article explique comment arrêter/démarrer des opérations dans Azure Database pour PostgreSQL à l’aide du portail Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: a08c9244644228bd6d9154bdc4882fc91334bc62
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797371"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview-using-azure-portal"></a>Arrêter/démarrer un serveur flexible Azure Database pour PostgreSQL (préversion) à l’aide du Portail Azure

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en actuellement en préversion.

Cet article fournit des instructions pas à pas pour arrêter et démarrer un serveur flexible.

## <a name="pre-requisites"></a>Conditions préalables

Pour utiliser ce guide pratique, il vous faut :

-   Vous devez disposer d’un serveur flexible Azure Database pour PostgreSQL.

## <a name="stop-a-running-server"></a>Arrêter un serveur en cours d’exécution

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à arrêter.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Arrêter** dans la barre d’outils.

> [!NOTE]
> Une fois le serveur arrêté, aucune autre opération de gestion n’est disponible pour le serveur flexible.

Notez que les serveurs arrêtés redémarreront automatiquement après sept jours. Toutes les mises à jour de maintenance en attente seront appliquées lors du prochain démarrage du serveur.

## <a name="start-a-stopped-server"></a>Démarrer un serveur arrêté

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à démarrer.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Démarrer** dans la barre d’outils.

> [!NOTE]
> Une fois que le serveur a démarré, toutes les opérations de gestion sont disponibles pour le serveur flexible.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [options de calcul et de stockage dans Azure Database pour PostgreSQL - Serveur flexible](./concepts-compute-storage.md).
