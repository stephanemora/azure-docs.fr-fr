---
title: 'Démarrage rapide : Créer un groupe de serveurs de niveau de base - Hyperscale (Citus) - Azure Database pour PostgreSQL'
description: Démarrez avec le niveau de base Hyperscale (Citus) d’Azure Database pour PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/03/2021
ms.openlocfilehash: 88bf816ca5cad294a7ab0592c80420f20ade4ed5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728705"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Créer un groupe de serveurs de niveau de base Hyperscale (Citus) dans le portail Azure

Azure Database pour PostgreSQL - Hyperscale (Citus) est un service managé que vous utilisez pour exécuter, gérer et mettre à l’échelle des bases de données PostgreSQL hautement disponibles dans le cloud. Son [niveau de base](concepts-hyperscale-tiers.md) est une option de déploiement pratique pour le développement et les tests initiaux.

Ce guide de démarrage rapide vous montre comment créer un groupe de serveurs de niveau de base Hyperscale (Citus) avec le portail Azure. Vous allez provisionner le groupe de serveurs et vérifier que vous pouvez vous y connecter pour exécuter des requêtes.

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment configurer un groupe de serveurs Hyperscale (Citus). Vous vous y êtes connecté avec psql, vous avez créé un schéma et vous avez distribué les données.

- Suivre un tutoriel pour [créer des applications multilocataires scalables](./tutorial-design-database-hyperscale-multi-tenant.md)
- Déterminer la [taille initiale](howto-hyperscale-scale-initial.md) optimale pour votre groupe de serveurs
