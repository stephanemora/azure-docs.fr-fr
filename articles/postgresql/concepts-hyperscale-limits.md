---
title: Limites et restrictions – Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Limites actuelles pour les groupes de serveurs Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 5bf02173d105ab81807bdc4ee68e3b8f9bc8e0a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532580"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Limites et restrictions d’Azure Database pour PostgreSQL – Hyperscale (Citus)

La section suivante décrit les limites fonctionnelles et les limites de capacités du service Hyperscale (Citus).

## <a name="maximum-connections"></a>Nombre maximal de connexions

Chaque connexion PostgreSQL (même inactive) utilise au moins 10 Mo de mémoire. Il est donc important de limiter les connexions simultanées. Voici les limites que nous avons choisies pour maintenir l’intégrité des nœuds :

* Nœud coordinateur
   * Nombre maximal de connexions
       * 300 pour 0-3 vCores
       * 500 pour 4-15 vCores
       * 1000 pour 16 vCores ou plus
   * Nombre maximal de connexions utilisateur
       * 297 pour 0 à 3 vCores
       * 497 pour 4 à 15 vCores
       * 997 pour 16 vCores ou plus
* Nœud Worker
   * Nombre maximal de connexions
       * 600

Au-delà de ces limites, les tentatives de connexion échouent et génèrent une erreur. Le système réserve trois connexions pour les nœuds de surveillance. C’est pourquoi il y a trois connexions disponibles de moins pour les requêtes utilisateur que le total des connexions.

### <a name="connection-pooling"></a>Regroupement de connexions

Vous pouvez augmenter davantage le nombre de connexions en utilisant le [regroupement de connexions](concepts-hyperscale-connection-pool.md). Hyperscale (Citus) offre un regroupeur de connexions pgBouncer managé, configuré pour jusqu’à 2 000 connexions clientes simultanées.

## <a name="storage-scaling"></a>Mise à l’échelle du stockage

Il est possible d’effectuer un scale-up du stockage sur les nœuds coordinateur et Worker, mais non un scale-down.

## <a name="storage-size"></a>Taille de stockage

Les nœuds coordinateur et Worker prennent en charge jusqu’à 2 Tio de stockage. Pour connaître les tailles de cluster et de nœud, consultez [ci-dessus](concepts-hyperscale-configuration-options.md#compute-and-storage) les options de stockage disponibles et le calcul des IOPS.

## <a name="database-creation"></a>Création de base de données

Le portail Azure fournit des informations d’identification pour se connecter à une seule base de données par groupe de serveurs Hyperscale (Citus), la base de données `citus`. La création d’une autre base de données n’est actuellement pas autorisée, et la commande CREATE DATABASE échoue avec une erreur.

## <a name="columnar-storage"></a>Stockage en colonnes

Hyperscale (Citus) présente actuellement ces limitations avec les [tables en colonnes](concepts-hyperscale-columnar.md) :

* La compression est sur le disque, pas en mémoire
* Ajout uniquement (pas de prise en charge de la mise à jour/suppression)
* Aucune récupération de l’espace (par exemple, les transactions restaurées peuvent toujours consommer de l’espace disque)
* Aucune prise en charge de l’index, de l’analyse d’index ou de l’analyse d’index bitmap
* Aucun tidscan
* Aucun exemple d’analyse
* Aucune prise en charge de TOAST (grandes valeurs prises en charge inline)
* Aucune prise en charge pour les instructions ON CONFLICT (à l’exception des actions DO NOTHING sans cible spécifiée).
* Aucune prise en charge des verrous de tuple (SELECT... FOR SHARE, SELECT... FOR UPDATE)
* Aucune prise en charge du niveau d’isolation sérialisable
* Prise en charge des versions de serveur PostgreSQL 12 et ultérieures uniquement
* Aucune prise en charge des clés étrangères, des contraintes uniques ou des contraintes d’exclusion
* Aucune prise en charge du décodage logique
* Aucune prise en charge des analyses parallèles intra-nœud
* Aucune prise en charge pour les déclencheurs AFTER... FOR EACH ROW
* Aucune table en colonnes UNLOGGED
* Aucune table en colonnes TEMPORARY

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un groupe de serveurs Hyperscale (Citus) dans le portail](quickstart-create-hyperscale-portal.md).
* Découvrez comment activer le [regroupement de connexions](concepts-hyperscale-connection-pool.md).
