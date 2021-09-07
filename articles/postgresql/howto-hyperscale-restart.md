---
title: Redémarrer le serveur - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Comment redémarrer la base de données dans Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 7/9/2021
ms.openlocfilehash: 0de8108cde0b5017221bf3ab038d98c98bb94546
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598620"
---
# <a name="restart-azure-database-for-postgresql---hyperscale-citus"></a>Redémarrer Azure Database pour PostgreSQL - Hyperscale (Citus)

Si vous voulez redémarrer votre groupe de serveurs Hyperscale (Citus), vous pouvez le faire à partir de la page **Vue d’ensemble** du groupe dans le portail Azure. Sélectionnez le bouton **Redémarrer** dans la barre du haut. Une boîte de dialogue de confirmation apparaît. Sélectionnez **Redémarrer tout** pour continuer.

> [!NOTE]
> Si le bouton Redémarrer n’est pas encore présent pour votre groupe de serveurs, ouvrez une demande de support Azure pour redémarrer le groupe de serveurs.

Le redémarrage du groupe de serveurs s’applique à tous les nœuds : vous ne pouvez pas redémarrer sélectivement des nœuds individuels. Le redémarrage s’applique aux machines virtuelles entières des nœuds, et pas seulement aux instances de serveur PostgreSQL. Les applications qui tentent d’utiliser la base de données vont connaître des temps d’arrêt de connectivité pendant le redémarrage.

**Étapes suivantes**

- La modification de certains paramètres de serveur nécessite un redémarrage. Consultez la liste de [tous les paramètres de serveur](reference-hyperscale-parameters.md) configurables sur Hyperscale (Citus).
