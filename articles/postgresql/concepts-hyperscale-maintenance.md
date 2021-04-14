---
title: Maintenance planifiée – Azure Database pour PostgreSQL – Hyperscale (Citus)
description: Cet article décrit la fonction de maintenance planifiée dans Azure Database pour PostgreSQL – Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027458"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Maintenance planifiée dans Azure Database pour PostgreSQL – Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) effectue une maintenance périodique pour garantir la sécurité, la stabilité et la mise à jour de votre base de données managée.  Pendant la maintenance, tous les nœuds du groupe de serveurs reçoivent de nouvelles fonctionnalités, des mises à jour et des correctifs.

Les principales fonctionnalités de la maintenance planifiée pour Hyperscale (Citus) sont les suivantes :

* Les mises à jour sont appliquées en même temps sur tous les nœuds du groupe de serveurs.
* Les notifications concernant la maintenance à venir sont publiées sur Azure Service Health cinq jours à l’avance.
* Il faut habituellement compter au moins 30 jours entre les événements de maintenance réussis pour un groupe de serveurs.

## <a name="notification-about-upcoming-maintenance"></a>Notification concernant la maintenance à venir

Les notifications concernant la maintenance planifiée à venir sont publiées sur Azure Service Health et peuvent être :

* Envoyées par e-mail à une adresse spécifique.
* Envoyées par e-mail à un rôle Azure Resource Manager.
* Envoyées dans un message texte (SMS) à des appareils mobiles.
* Envoyées en tant que notification à une application Azure.
* Remises sous forme de message vocal.

> [!IMPORTANT]
> Normalement, il faut compter au moins 30 jours entre les événements de maintenance planifiée réussis pour un groupe de serveurs.
>
> Toutefois, dans le cas d’une mise à jour critique urgente telle qu’une vulnérabilité grave, la fenêtre de notification peut être inférieure à cinq jours. La mise à jour critique peut être appliquée à votre serveur même si une maintenance planifiée réussie a été effectuée au cours des 30 derniers jours.

Si la maintenance échoue ou si elle est annulée, le système crée une notification.
Il tentera à nouveau d’effectuer la maintenance en fonction des paramètres actuels de planification et vous informera cinq jours avant le prochain événement de maintenance.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [recevoir des notifications sur la maintenance à venir](../service-health/service-notifications.md) à l’aide d’Azure Service Health.
* Découvrez comment [configurer des alertes concernant les événements de maintenance planifiée à venir](../service-health/resource-health-alert-monitor-guide.md).
