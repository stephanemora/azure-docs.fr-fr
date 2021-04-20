---
title: Limites et restrictions – Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Limites actuelles pour les groupes de serveurs Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023814"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Limites et restrictions d’Azure Database pour PostgreSQL – Hyperscale (Citus)

La section suivante décrit les limites fonctionnelles et les limites de capacités du service Hyperscale (Citus).

## <a name="maximum-connections"></a>Nombre maximal de connexions

Chaque connexion PostgreSQL (même inactive) utilise au moins 10 Mo de mémoire. Il est donc important de limiter les connexions simultanées. Voici les limites que nous avons choisies pour maintenir l’intégrité des nœuds :

* Nœud coordinateur
   * Nombre maximal de connexions : 300
   * Nombre maximal de connexions utilisateur : 297
* Nœud Worker
   * Nombre maximal de connexions : 600
   * Nombre maximal de connexions utilisateur : 597

> [!NOTE]
> Dans un groupe de serveurs dont les [fonctionnalités en préversion](hyperscale-preview-features.md) sont activées, les limites de connexion au coordinateur sont légèrement différentes :
>
> * Connexions maximales du nœud coordinateur
>    * 300 pour 0-3 vCores
>    * 500 pour 4-15 vCores
>    * 1000 pour 16 vCores ou plus

Au-delà de ces limites, les tentatives de connexion échouent et génèrent une erreur. Le système réserve trois connexions pour les nœuds de surveillance. C’est pourquoi il y a trois connexions disponibles de moins pour les requêtes utilisateur que le total des connexions.

### <a name="connection-pooling"></a>Regroupement de connexions

L’établissement de nouvelles connexions prend du temps. Ce point est problématique pour la plupart des applications, qui demandent de nombreuses connexions de courte durée. Nous vous recommandons d’utiliser un dispositif de regroupement de connexions pour réduire les transactions inactives et réutiliser les connexions existantes. Pour en savoir plus, consultez notre [billet de blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Vous pouvez exécuter votre propre pool de connexions, ou utiliser PgBouncer géré par Azure.

#### <a name="managed-pgbouncer-preview"></a>PgBouncer géré (préversion)

> [!IMPORTANT]
> Le regroupement de connexions PgBouncer géré dans Hyperscale (Citus) est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Vous pouvez consulter la liste complète des nouvelles fonctionnalités dans les [fonctionnalités d’évaluation pour Hyperscale (Citus)](hyperscale-preview-features.md).

Les regroupeurs de connexion tels que PgBouncer permettent à un plus grand nombre de clients de se connecter en même temps au nœud coordinateur. Les applications se connectent au regroupement et le regroupement relaie les commandes vers la base de données de destination.

Lorsque les clients se connectent par le biais de PgBouncer, le nombre de connexions qui peuvent s’exécuter activement dans la base de données ne change pas. Au lieu de cela, PgBouncer met en file d’attente les connexions en excès et les exécute lorsque la base de données est prête.

Hyperscale (Citus) offre désormais une instance gérée PgBouncer pour les groupes de serveurs (en préversion). Il prend en charge jusqu’à 2 000 connexions clientes simultanées.
Pour vous connecter par le biais de PgBouncer, procédez comme suit :

1. Accédez à la page **Chaînes de connexion** de votre groupe de serveurs dans le portail Azure.
2. Activez la case à cocher **Chaînes de connexion PgBouncer**. (Les chaînes de connexion répertoriées varient.)
3. Mettez à jour les applications clientes pour qu’elles se connectent à la nouvelle chaîne.

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

Découvrez comment [créer un groupe de serveurs Hyperscale (Citus) dans le portail](quickstart-create-hyperscale-portal.md).
