---
title: Maintenance planifiée – Azure Database pour PostgreSQL – Hyperscale (Citus)
description: Cet article décrit la fonction de maintenance planifiée dans Azure Database pour PostgreSQL – Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106783"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Maintenance planifiée dans Azure Database pour PostgreSQL – Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) effectue une maintenance périodique pour garantir la sécurité, la stabilité et la mise à jour de votre base de données managée.  Pendant la maintenance, tous les nœuds du groupe de serveurs reçoivent de nouvelles fonctionnalités, des mises à jour et des correctifs.

Les principales fonctionnalités de la maintenance planifiée pour Hyperscale (Citus) sont les suivantes :

* Les mises à jour sont appliquées en même temps sur tous les nœuds du groupe de serveurs.
* Les notifications concernant la maintenance à venir sont publiées sur Azure Service Health cinq jours à l’avance.
* Il faut habituellement compter au moins 30 jours entre les événements de maintenance réussis pour un groupe de serveurs.
* Le jour par défaut de la semaine et la fenêtre de temps durant cette journée pour le démarrage de la maintenance peuvent être définis individuellement pour chaque groupe de serveurs

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Sélection d’une fenêtre de maintenance et notification concernant la maintenance à venir

Vous pouvez planifier la maintenance pour un jour spécifique de la semaine et une fenêtre de temps durant cette journée. Vous pouvez aussi laisser le système choisir un jour et une fenêtre de temps pour vous automatiquement. Dans les deux cas, le système vous alerte cinq jours avant d’exécuter une opération de maintenance. Le système indique également quand la maintenance est démarrée et quand elle est terminée.

Les notifications concernant la maintenance planifiée à venir sont publiées sur Azure Service Health et peuvent être :

* Envoyées par e-mail à une adresse spécifique.
* Envoyées par e-mail à un rôle Azure Resource Manager.
* Envoyées dans un message texte (SMS) à des appareils mobiles.
* Envoyées en tant que notification à une application Azure.
* Remises sous forme de message vocal.

Quand vous spécifiez des préférences de planification de la maintenance, vous pouvez choisir un jour de la semaine et une fenêtre de temps. Si vous n’en spécifiez pas, le système choisit des horaires compris entre 23h00 et 7h00 dans le fuseau horaire de la région de votre groupe de serveurs. Vous pouvez définir différentes planifications pour chaque groupe de serveurs Hyperscale (Citus) dans votre abonnement Azure.

> [!IMPORTANT]
> Normalement, il faut compter au moins 30 jours entre les événements de maintenance planifiée réussis pour un groupe de serveurs.
>
> Toutefois, dans le cas d’une mise à jour critique urgente telle qu’une vulnérabilité grave, la fenêtre de notification peut être inférieure à cinq jours. La mise à jour critique peut être appliquée à votre serveur même si une maintenance planifiée réussie a été effectuée au cours des 30 derniers jours.

Vous pouvez mettre à jour les paramètres de planification à tout moment. Si une maintenance est planifiée pour votre groupe de serveurs Hyperscale (Citus) et que vous mettez à jour la planification, les événements existants se poursuivront comme prévu initialement. La modification des paramètres prendra effet après l’achèvement des événements existants.

Si la maintenance échoue ou si elle est annulée, le système crée une notification.
Il tentera à nouveau d’effectuer la maintenance en fonction des paramètres actuels de planification et vous informera cinq jours avant le prochain événement de maintenance.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [modifier la planification de la maintenance](howto-hyperscale-maintenance.md).
* Découvrez comment [recevoir des notifications sur la maintenance à venir](../service-health/service-notifications.md) à l’aide d’Azure Service Health.
* Découvrez comment [configurer des alertes concernant les événements de maintenance planifiée à venir](../service-health/resource-health-alert-monitor-guide.md).
