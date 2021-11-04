---
title: Azure Database pour PostgreSQL - Notes de publication sur le serveur flexible
description: Notes de publication sur Azure Database pour PostgreSQL - Serveur flexible
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2021
ms.openlocfilehash: 24b5db0411241778ce9afd63a4a167b9d6d483eb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433414"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>Notes de publication - Azure Database pour PostgreSQL - Serveur flexible

Cette page fournit les toutes dernières informations concernant les ajouts de fonctionnalités, les versions de moteur prises en charge et les extensions. Elle inclut également toutes les annonces relatives au serveur flexible - PostgreSQL.

> [!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est en préversion

## <a name="release-september-2021"></a>Version : septembre 2021

* Prise en charge de [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server).
* Prise en charge des [nouvelles régions](overview.md#azure-regions) « Inde Centre » et « Japon Ouest ».
* Prise en charge du mode de connectivité non SSL avec le nouveau paramètre de serveur `require_secure_transport`.
* Prise en charge du paramètre de serveur `log_line_prefix` qui ajoute la chaîne au début de chaque ligne de journal.
* Prise en charge de [Azure Resource Health](../../service-health/resource-health-overview.md) pour le diagnostic de l’intégrité du serveur flexible et pour obtenir de l’aide.
* Plusieurs corrections de bogues, et amélioration de la stabilité et des performances.

## <a name="release-july-2021"></a>Publication : Juillet 2021

* Prise en charge des [nouvelles régions](overview.md#azure-regions) Asie Est, Allemagne Centre-Ouest, Corée Sud, USA Centre Sud, Royaume-Uni Ouest.
* Prise en charge de l’[extension pglogical](concepts-logical.md) v2.3.2 avec PostgreSQL 11, 12 et 13.<sup>$</sup>
* PgBouncer inclut désormais `ignore_startup_parameters` pour ignorer certains paramètres du pilote côté client, notamment les paramètres `extra_float_digits` et `pgbouncer.query_wait_timeout`.  <sup>$</sup>.
* Prise en charge de `pg_stat_reset_shared('bgwriter');` et `pg_stat_reset_shared('archiver');` pour réinitialiser les compteurs affichés dans les vues `pg_stat_bgwriter` et `pg_stat_archiver` <sup>$</sup>.
* Plusieurs corrections de bogues, et amélioration de la stabilité et des performances<sup>$</sup>.

<sup> **$**</sup> Les nouveaux serveurs obtiennent ces fonctionnalités automatiquement. Dans vos serveurs existants, ces fonctionnalités sont activées pendant la prochaine fenêtre de maintenance de votre serveur.

## <a name="release-june-2021"></a>Version : juin 2021

* Prise en charge des [dernières versions mineures de PostgreSQL](./concepts-supported-versions.md) 13.3, 12.7 et 11.12 avec les nouvelles créations de serveur<sup>$</sup>.
* Support pour de [nouvelles régions](overview.md#azure-regions), notamment Australie Sud-Est, Brésil Sud, Corée Centre, Norvège Est, Afrique du Sud Nord, Suisse Nord, Émirats arabes unis Nord et USA Ouest.
* Support des fonctionnalités de [basculement à la demande](./concepts-high-availability.md#on-demand-failover), notamment le basculement forcé et le basculement planifié pour les déploiements à haute disponibilité redondants dans une zone.
* La prise en charge de l’[authentification SCRAM](how-to-connect-scram.md) pour toutes les versions majeures avec le nouveau serveur crée <sup>$</sup>.
* La prise en charge de `pg_prewarm` doit être préchargée à l’aide de `shared_preload_libraries` avec les nouvelles créations de serveur<sup>$</sup>.
* Prise en charge de l’extension lo. Consultez la [page des extensions](./concepts-extensions.md) pour les versions prises en charge avec chaque version majeure <sup>$</sup>.
* Plusieurs corrections de bogues, et amélioration de la stabilité et des performances<sup>$</sup>.
  
<sup> **$**</sup> Les nouveaux serveurs obtiennent ces fonctionnalités automatiquement.  Vos serveurs existants seront automatiquement mis à niveau vers la dernière version mineure prise en charge et les nouvelles fonctionnalités seront également activées lors de la future fenêtre de maintenance de votre serveur.

## <a name="release-may-2021"></a>Version : mai 2021

* Prise en charge de la [version majeure 13 de PostgreSQL](./concepts-supported-versions.md).
* Prise en charge des extensions, notamment pg_partman, pg_cron et pgaudit. Consultez la [page des extensions](./concepts-extensions.md) pour les versions prises en charge avec chaque version majeure.
* Plusieurs corrections de bogues, et amélioration de la stabilité et des performances.

## <a name="release-april-2021"></a>Version : avril 2021

* Prise en charge des [dernières versions mineures de PostgreSQL](./concepts-supported-versions.md) 12.6 et 11.11 avec les nouvelles créations de serveur
* Prise en charge de la [zone DNS privée](./concepts-networking.md#private-access-vnet-integration) du réseau virtuel
* Prise en charge du choix de la zone de disponibilité pendant l’opération de récupération jusqu’à une date et heure
* Prise en charge de nouvelles [régions](./overview.md#azure-regions), y compris Australie Est, Canada Centre et France Centre
* Prise en charge du pooler de connexions [PgBouncer intégré](./concepts-pgbouncer.md) 
<!--- * Support for [pglogical](https://github.com/2ndQuadrant/pglogical) extension version 2.3.2. -->
* [Performances intelligentes](concepts-query-store.md) en préversion
* Plusieurs corrections de bogues et amélioration de la stabilité et des performances

## <a name="contacts"></a>Contacts

Pour toute question ou suggestion au sujet du serveur flexible Azure Database pour PostgreSQL, envoyez un e-mail à l’équipe Azure Database pour PostgreSQL ([@Ask Azure DB pour PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Notez que cette adresse e-mail n’est pas un alias de support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0).
  

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu l'introduction au mode de déploiement Azure Database pour PostgreSQL - Serveur flexible, vous êtes prêt à créer votre premier serveur : [Créer une instance Azure Database pour PostgreSQL - Serveur flexible à l’aide du portail Azure](./quickstart-create-server-portal.md)
