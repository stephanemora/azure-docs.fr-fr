---
title: 'Démarrage rapide : Créer un groupe de serveurs de niveau de base - Hyperscale (Citus) - Azure Database pour PostgreSQL'
description: Démarrez avec le niveau de base Hyperscale (Citus) d’Azure Database pour PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023823"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Créer un groupe de serveurs de niveau de base Hyperscale (Citus) dans le portail Azure

Azure Database pour PostgreSQL - Hyperscale (Citus) est un service managé que vous utilisez pour exécuter, gérer et mettre à l’échelle des bases de données PostgreSQL hautement disponibles dans le cloud. Son [niveau de base](concepts-hyperscale-tiers.md) est une option de déploiement pratique pour le développement et les tests initiaux.

Ce guide de démarrage rapide vous montre comment créer un groupe de serveurs de niveau de base Hyperscale (Citus) avec le portail Azure. Vous allez provisionner le groupe de serveurs et vérifier que vous pouvez vous y connecter pour exécuter des requêtes.

> [!IMPORTANT]
> Le niveau de base Hyperscale (Citus) est actuellement en préversion.  Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Vous pouvez consulter la liste complète des nouvelles fonctionnalités dans [Fonctionnalités d’évaluation pour Hyperscale (Citus)](hyperscale-preview-features.md).

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment configurer un groupe de serveurs Hyperscale (Citus). Vous vous y êtes connecté avec psql, vous avez créé un schéma et vous avez distribué les données.

- Suivre un tutoriel pour [créer des applications multilocataires scalables](./tutorial-design-database-hyperscale-multi-tenant.md)
- Déterminer la [taille initiale](howto-hyperscale-scale-initial.md) optimale pour votre groupe de serveurs
