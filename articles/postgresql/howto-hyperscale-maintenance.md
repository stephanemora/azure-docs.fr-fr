---
title: Azure Database pour PostgreSQL - Hyperscale (Citus) - Maintenance planifiée - Portail Azure
description: Apprenez à configurer les paramètres de maintenance planifiée pour une instance d'Azure Database pour PostgreSQL - Hyperscale (Citus) à partir du portail Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8e22de5288380490490c91846322e418f98dfcd4
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108317253"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Gérer les paramètres de maintenance planifiée pour Azure Database pour PostgreSQL - Hyperscale (Citus)

Vous pouvez spécifier des options de maintenance pour chacun des groupes de serveurs Hyperscale (Citus) de votre abonnement Azure. Les options incluent la planification de maintenance et les paramètres de notification pour les événements de maintenance à venir et terminés.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Un [groupe de serveurs Azure Database pour PostgreSQL - Hyperscale (Citus)](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Spécifier les options de planification de la maintenance

1. Sur la page du groupe de serveurs Hyperscale (Citus), sous le titre **Paramètres**, choisissez **Maintenance** pour ouvrir les options de maintenance planifiée.
2. La planification par défaut (gérée par le système) correspond à un jour aléatoire de la semaine et à une fenêtre de 30 minutes pour le début la maintenance entre 23h et 7h, [heure de la région Azure](https://go.microsoft.com/fwlink/?linkid=2143646) du groupe de serveurs. Si vous souhaitez personnaliser cette planification, choisissez **Planification personnalisée**. Vous pouvez ensuite sélectionner le jour de la semaine de votre choix, et une fenêtre de 30 minutes pour l'heure de début de la maintenance.

## <a name="notifications-about-scheduled-maintenance-events"></a>Notifications relatives aux événements de maintenance planifiée

Vous pouvez utiliser Azure Service Health pour [afficher les notifications](../service-health/service-notifications.md) relatives aux maintenances planifiées passées et à venir sur votre groupe de serveurs Hyperscale (Citus). Vous pouvez également [configurer](../service-health/resource-health-alert-monitor-guide.md) des alertes dans Azure Service Health pour obtenir des notifications sur les événements de maintenance.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [maintenance planifiée dans Azure Database pour PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-maintenance.md)
* En savoir plus sur [Azure Service Health](../service-health/overview.md)
