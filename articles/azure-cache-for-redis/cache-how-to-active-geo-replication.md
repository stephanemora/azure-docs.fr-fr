---
title: Configurer la géoréplication active pour les instances Azure Cache pour Redis Enterprise
description: Découvrez comment répliquer vos instances Azure Cache pour Redis Enterprise dans des régions Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: d52998e477e44f89a35a70f7e2b5d49cbc68e386
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660217"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Configurer la géoréplication active pour les instances Azure Cache pour Redis Enterprise (préversion)

Dans cet article, vous allez voir comment configurer un cache Azure avec géoréplication active à l’aide du portail Azure.

La géoréplication active regroupe deux instances (ou plus) Azure Cache pour Redis Enterprise dans un même cache qui s’étend sur plusieurs régions Azure. Toutes les instances agissent comme des instances principales locales. Une application détermine quelle(s) instance(s) utiliser pour les demandes de lecture et d’écriture.

## <a name="create-or-join-an-active-geo-replication-group"></a>Créer ou rejoindre un groupe de géoréplication active

> [!IMPORTANT]
> La géoréplication active doit être activée au moment de la création d’une instance Azure Cache pour Redis.
>
>

1. Dans l’interface utilisateur de création **Nouveau cache Redis**, cliquez sur **Configurer** pour configurer la **Géoréplication active** sous l’onglet **Avancé**.

    ![Configurer la géoréplication active](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Créez un groupe de réplication pour une première instance de cache, ou sélectionnez-en un dans la liste.

    ![Lier des caches](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Cliquez sur **Configurer** pour terminer.

    ![Lier des caches](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Répétez les étapes ci-dessus pour toutes les autres instances de cache du groupe de géoréplication.

## <a name="remove-from-an-active-geo-replication-group"></a>Supprimer une instance d’un groupe de géoréplication active

Pour supprimer une instance de cache d’un groupe de géoréplication active, supprimez simplement l’instance. Les instances restantes seront automatiquement reconfigurées.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctionnalités d’Azure Cache pour Redis.

* [Niveaux de service Azure Cache pour Redis](cache-overview.md#service-tiers)
* [Haute disponibilité pour Azure Cache pour Redis](cache-high-availability.md)
