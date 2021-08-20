---
title: Fonctionnalités en préversion dans Azure Database pour PostgreSQL - Hyperscale (Citus)
description: Liste mise à jour des fonctionnalités actuellement en préversion
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 07/09/2021
ms.openlocfilehash: 1e0f7c133a0ac0091355cba20d251333e6402212
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463536"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>Fonctionnalités en préversion pour PostgreSQL - Hyperscale (Citus)

Azure Database pour PostgreSQL - Hyperscale (Citus) propose des préversions pour les fonctionnalités non publiées. Les préversions sont fournies sans contrat de niveau de service et sont déconseillées pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.  Pour plus d’informations, consultez [Conditions d’utilisation supplémentaires relatives aux préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features-currently-in-preview"></a>Fonctionnalités actuellement en préversion

Voici les fonctionnalités actuellement disponibles pour la préversion :

* Niveau **[De base](concepts-hyperscale-tiers.md)** . Exécution d’un groupe de serveurs en utilisant uniquement un nœud coordinateur et aucun nœud Worker. Moyen économique d’effectuer les premiers tests et développements, et de gérer de petites charges de travail de production.
* **[PostgreSQL 12 et 13](concepts-hyperscale-versions.md)** .
  Utilisation de la toute dernière version de base de données dans votre groupe de serveurs.
* **[CITUS 10](concepts-hyperscale-versions.md#citus-and-other-extension-versions)** .
  Installé automatiquement sur les groupes de serveurs qui exécutent PostgreSQL 13.
* **[Stockage en colonnes](concepts-hyperscale-columnar.md)** .
  Stockage des colonnes des tables sélectionnées (plutôt que les lignes) de façon contiguë sur le disque. Prend en charge la compression sur disque. Adapté aux charges de travail d’analytique et d’entreposage de données.
* **[Réplicas en lecture](howto-hyperscale-read-replicas-portal.md)** (actuellement dans la même région uniquement). Toutes les modifications apportées au groupe principal de serveurs sont reflétées dans son réplica, tandis que les requêtes sur le réplica n’entraînent aucune charge supplémentaire sur le groupe d’origine.
  Les réplicas représentent un outil utile pour améliorer les performances des charges de travail en lecture seule.
* **[PgBouncer managé](concepts-hyperscale-connection-pool.md)** .
  Dispositif de regroupement de connexions qui permet à de nombreux clients de se connecter à un groupe de serveurs en même temps, tout en limitant le nombre de connexions actives. Il satisfait aux requêtes de connexion tout en conservant une exécution fluide du nœud coordinateur.
* **[pgAudit](concepts-hyperscale-audit.md)** . Fournit une journalisation détaillée des audits de session et d’objets via la fonctionnalité de journalisation PostgreSQL standard. Elle génère les journaux d’audit nécessaires pour passer certains audits administratifs, financiers ou de certification ISO.
* **[create_extension() UDF](concepts-hyperscale-extensions.md#use-postgresql-extensions)** .
  Permet de créer des extensions dont l’installation nécessite un accès administratif.

### <a name="available-regions-for-preview-features"></a>Régions disponibles pour les fonctionnalités en préversion

L’extension pgAudit est disponible dans toutes les [régions prises en charge par Hyperscale (Citus)](concepts-hyperscale-configuration-options.md#regions).
Les autres fonctionnalités en préversion sont disponibles uniquement dans la région **USA Est**.

## <a name="does-my-server-group-have-access-to-preview-features"></a>Mon groupe de serveurs a-t-il accès aux fonctionnalités en préversion ?

Pour déterminer si les fonctionnalités en préversion de votre groupe de serveurs Hyperscale (Citus) sont activées, accédez à la page **Vue d’ensemble** du groupe de serveurs dans le portail Azure.
Si vous voyez la propriété **Niveau : De base (préversion)** ou **Niveau : Standard (préversion)** , votre groupe de serveurs a accès aux fonctionnalités en préversion.

### <a name="how-to-get-access"></a>Comment obtenir l’accès

Lorsque vous créez un groupe de serveurs Hyperscale (Citus), cochez la case **Activer les fonctionnalités en préversion**.

## <a name="contact-us"></a>Nous contacter

Faites-nous part de votre expérience d’utilisation des fonctionnalités en préversion, par e-mail à [Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
(Cette adresse e-mail n’est pas un canal du support technique. Pour les problèmes techniques, ouvrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).)
