---
title: Arrêt/Démarrage - Portail Azure - Serveur flexible Azure Database pour MySQL
description: Cet article explique comment arrêter/démarrer des opérations dans Azure Database pour MySQL à l’aide du portail Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: b9f406035d32a9af9ba2f5b085bcaca1b51e9d92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929851"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Arrêter/Démarrer un serveur flexible Azure Database pour MySQL (préversion)


> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Cet article indique la procédure pas-à-pas à suivre pour arrêter et démarrer le serveur flexible.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

-   Vous devez disposer d’un serveur flexible Azure Database pour MySQL.

## <a name="stop-a-running-server"></a>Arrêter un serveur en cours d’exécution

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à arrêter.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Arrêter** dans la barre d’outils.

> [!NOTE]
> Une fois que le serveur est arrêté, les autres opérations de gestion ne sont pas disponibles pour le serveur flexible.

## <a name="start-a-stopped-server"></a>Démarrer un serveur arrêté

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à démarrer.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Arrêter** dans la barre d’outils.

> [!NOTE]
> Une fois que le serveur a démarré, toutes les opérations de gestion sont disponibles pour le serveur flexible.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [réseaux dans un serveur flexible Azure Database pour MySQL](./concepts-networking.md)
- [Créer et gérer un réseau virtuel de serveur flexible Azure Database pour MySQL avec le portail Azure](./how-to-manage-virtual-network-portal.md).

