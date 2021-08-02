---
title: Résoudre les problèmes d’accès en lecture seule - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Découvrir pourquoi un groupe de serveurs Hyperscale (Citus) peut passer en lecture seule et ce qu’il faut faire
keywords: connexion postgresql, lecture seule
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 6/4/2021
ms.openlocfilehash: 13d0c630c38e71b243a32404b26e3a141a20a6b2
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111561654"
---
# <a name="troubleshoot-read-only-access-to-azure-database-for-postgresql---hyperscale-citus"></a>Résoudre les problèmes d’accès en lecture seule à Azure Database pour PostgreSQL - Hyperscale (Citus)

PostgreSQL ne peut pas s’exécuter sur un ordinateur sans espace disque libre. Pour maintenir l’accès aux serveurs PostgreSQL, il est nécessaire de ne pas arriver à court d’espace disque.

Dans Hyperscale (Citus), les nœuds sont définis avec un état en lecture seule (RO) quand le disque est presque plein. Le fait d’interdire les écritures empêche le disque de continuer à se remplir et assure la disponibilité du nœud pour les lectures. Durant l’état en lecture seule, vous pouvez prendre des mesures pour libérer davantage d’espace disque.

Plus précisément, un nœud Hyperscale (Citus) passe en lecture seule lorsqu’il a moins de 5 Gio de stockage libre. Quand le serveur passe en lecture seule, toutes les sessions existantes sont déconnectées et les transactions non validées sont restaurées. Les opérations d’écriture et les validations de transaction échouent, tandis que les requêtes de lecture continuent de fonctionner.

## <a name="ways-to-recover-write-access"></a>Méthodes de récupération de l’accès en écriture

### <a name="on-the-coordinator-node"></a>Sur le nœud coordinateur

* [Augmenter la taille de stockage](howto-hyperscale-scale-grow.md#increase-storage-on-nodes) sur le nœud coordinateur, et/ou
* Distribuer des tables locales aux nœuds Worker ou supprimer des données. Quelle que soit l’option choisie, vous devez exécuter `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE` une fois que vous êtes connecté à la base de données et avant d’exécuter d’autres commandes.

### <a name="on-a-worker-node"></a>Sur un nœud Worker

* [Augmenter la taille de stockage](howto-hyperscale-scale-grow.md#increase-storage-on-nodes) sur les nœuds Worker et/ou
* [Rééquilibrer les données](howto-hyperscale-scale-rebalance.md) sur d’autres nœuds ou supprimer des données.
    * Quelle que soit l’option choisie, vous devez définir le nœud Worker en lecture-écriture temporairement. Envoyez une demande de support pour effectuer cette opération. Sinon, si vous exécutez un groupe de serveurs Hyperscale (Citus) en préversion, vous pouvez vous connecter directement aux nœuds Worker et utiliser `SET SESSION CHARACTERISTICS` comme décrit ci-dessus pour le nœud coordinateur.

## <a name="prevention"></a>Prévention

Nous vous recommandons de configurer une alerte pour vous avertir quand le serveur de stockage est proche du seuil. De cette façon, vous pouvez agir au plus tôt pour éviter la mise en lecture seule. Pour plus d’informations, consultez la documentation sur les [alertes recommandées](howto-hyperscale-alert-on-metric.md#suggested-alerts).

## <a name="next-steps"></a>Étapes suivantes

* [Configurez des alertes Azure](howto-hyperscale-alert-on-metric.md#suggested-alerts) pour obtenir une notification préalable afin de pouvoir prendre les mesures nécessaires avant la mise en lecture seule.
* Découvrez l’[utilisation des disques](https://www.postgresql.org/docs/current/diskusage.html) dans la documentation PostgreSQL.
* Découvrez les [caractéristiques des sessions](https://www.postgresql.org/docs/13/sql-set-transaction.html) dans la documentation PostgreSQL.
