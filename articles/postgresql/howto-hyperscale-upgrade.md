---
title: Mettre à niveau un groupe de serveurs - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Cet article explique la procédure de mise à niveau de PostgreSQL et Citus dans Azure Database pour PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023827"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Mettre à niveau un groupe de serveurs Hyperscale (Citus)

Ces instructions décrivent la procédure de mise à niveau vers une nouvelle version principale de PostgreSQL sur tous les nœuds de groupe de serveurs.

## <a name="test-the-upgrade-first"></a>Tester en premier lieu la mise à niveau

La mise à niveau de PostgreSQL entraîne plus de modifications que vous ne pouvez l’imaginer, car Hyperscale (Citus) met également à niveau les [extensions de base de données](concepts-hyperscale-extensions.md), notamment l’extension Citus.
Nous vous recommandons vivement de tester votre application avec les nouvelles versions de PostgreSQL et de Citus avant de mettre à niveau votre environnement de production.

Un moyen pratique de la tester consiste à effectuer une copie de votre groupe de serveurs à l’aide d’une [restauration à un instant dans le passé](concepts-hyperscale-backup.md#point-in-time-restore-pitr). Mettez à niveau la copie et testez votre application sur celle-ci. Une fois que vous avez vérifié que tout fonctionne correctement, mettez à niveau le groupe de serveurs original.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Mettre à niveau un groupe de serveurs dans le portail Azure

1. Dans la section **Présentation** d’un groupe de serveurs Hyperscale (Citus), sélectionnez le bouton **Mettre à niveau**.
1. Un dialogue s’affiche, indiquant la version actuelle de PostgreSQL et de Citus.
   Choisissez une nouvelle version de PostgreSQL dans la liste **Mettre à niveau vers**.
1. Vérifiez que la valeur de la **version Citus après la mise à niveau** est celle que vous attendez.
   Cette valeur change en fonction de la version de PostgreSQL que vous avez sélectionnée.
1. Sélectionnez le bouton **Mettre à niveau** pour continuer.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [les versions de PostgreSQL prises en charge](concepts-hyperscale-versions.md).
* Découvrez [les extensions](concepts-hyperscale-extensions.md) empaquetées avec chaque version de PostgreSQL dans un groupe de serveurs Hyperscale (Citus).
