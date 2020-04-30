---
title: Configurer la haute disponibilité - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Guide pratique pour activer ou désactiver la haute disponibilité
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 78e6e8c18e67ce2dff0de94d298f06693a40135f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584054"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurer la haute disponibilité Hyperscale (Citus)

Azure Database pour PostgreSQL - Hyperscale (Citus) fournit une haute disponibilité pour éviter les temps d’arrêt des bases de données. Quand la haute disponibilité est activée, un nœud de secours est attribué à chaque nœud d’un groupe de serveurs. Si le nœud d’origine n’est plus sain, son nœud de secours est promu pour le remplacer.

> [!IMPORTANT]
> Étant donné que la haute disponibilité double le nombre de serveurs d’un groupe, elle doublera également le coût.

Il est possible d’activer la haute disponibilité lors de la création d’un groupe de serveurs, ou par la suite sous l’onglet **Configurer** de votre groupe de serveurs dans le portail Azure. L’interface utilisateur est similaire dans les deux cas. Faites glisser le curseur de l’option **Haute disponibilité** sur OUI :

![Curseur de haute disponibilité](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Cliquez sur le bouton **Enregistrer** pour appliquer votre sélection. L’activation de la haute disponibilité peut prendre un certain temps, car le groupe de serveurs provisionne des nœuds de secours et leur envoie des données en streaming.

L’onglet **Vue d’ensemble** du groupe de serveurs liste tous les nœuds et leurs nœuds de secours, ainsi qu’une colonne **Haute disponibilité** indiquant si la haute disponibilité est correctement activée pour chaque nœud.

![Vue d’ensemble de la colonne Haute disponibilité dans le groupe de serveurs](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur la [haute disponibilité](concepts-hyperscale-high-availability.md).
