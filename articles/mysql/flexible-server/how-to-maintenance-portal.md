---
title: Azure Database pour MySQL – Serveur flexible (préversion) – Maintenance planifiée – Portail Azure
description: Découvrez comment configurer les paramètres de maintenance planifiée pour Azure Database pour MySQL – Serveur flexible à partir du portail Azure.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a4ff27243643a5c0fbb833a05a07e9e117196a6f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641109"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Gérer les paramètres de maintenance planifiée dans Azure Database pour MySQL – Serveur flexible

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Vous pouvez spécifier des options de maintenance pour chaque serveur flexible dans votre abonnement Azure. Les options incluent la planification de maintenance et les paramètres de notification pour les événements de maintenance à venir et terminés.

> [!IMPORTANT]
> Azure Database pour MySQL – Serveur flexible est en préversion.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Un [Azure Database pour MySQL – Serveur flexible](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Spécifier les options de planification de la maintenance
 
1. Dans la page du serveur MySQL, sous le titre **Paramètres**, choisissez **Maintenance** pour ouvrir les options de maintenance planifiée.
2. La planification par défaut (gérée par le système) est un jour aléatoire de la semaine et une fenêtre de 60 minutes pour le début la maintenance entre 23 h et 7 H, heure du serveur local. Si vous souhaitez personnaliser cette planification, choisissez **Planification personnalisée**. Vous pouvez ensuite sélectionner un jour par défaut de la semaine et une fenêtre de 60 minutes pour l’heure de début de la maintenance.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Notifications relatives aux événements de maintenance planifiée
 
Vous pouvez utiliser Azure Service Health pour [afficher les notifications](../../service-health/service-notifications.md) sur la maintenance planifiée à venir et effectuée sur votre serveur flexible. Vous pouvez également [configurer](../../service-health/resource-health-alert-monitor-guide.md) des alertes dans Azure Service Health pour obtenir des notifications sur les événements de maintenance.
 
## <a name="next-steps"></a>Étapes suivantes  
 
* Découvrir la [maintenance planifiée dans Azure Database pour MySQL – Serveur flexible](concepts-maintenance.md)
* En savoir plus sur [Azure Service Health](../../service-health/overview.md)
