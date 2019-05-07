---
title: Choisir les colonnes de distribution dans la base de données Azure pour PostgreSQL – très grande échelle (Citus) (version préliminaire)
description: Choix judicieux pour les colonnes de distribution dans les scénarios courants de très grande échelle
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078984"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Choisir les colonnes de distribution dans la base de données Azure pour PostgreSQL – très grande échelle (Citus) (version préliminaire)

Colonne de distribution de chaque table de choix est **une des plus importantes** décisions de modélisation. Hyperscale stocke les lignes dans les partitions en fonction de la valeur de colonne de distribution des lignes.

Les groupes de choix correct liés données ensemble sur les mêmes nœuds physiques, ce qui interroge une prise en charge rapide et ajout de toutes les fonctionnalités SQL. Un rend le système s’exécute lentement et ne prennent pas en charge toutes les fonctionnalités SQL sur les nœuds du choix incorrect.

Cette section propose des conseils de colonne de distribution pour les deux scénarios courants de très grande échelle.

### <a name="multi-tenant-apps"></a>Applications mutualisées

L’architecture mutualisée utilise une forme de base de données hiérarchique de modélisation pour distribuer les requêtes entre les nœuds dans le groupe de serveurs.  En haut de la hiérarchie de données est appelé le *id_locataire*et doivent être stockées dans une colonne sur chaque table.

Hyperscale inspecte les requêtes pour afficher le ID de locataire ils impliquent et recherche la partition de table correspondante. Il achemine la requête à un nœud worker unique qui contient la partition. Exécution d’une requête avec toutes les données pertinentes placées sur le même nœud, la colocalisation est appelée.

Le diagramme suivant illustre la colocalisation dans le modèle de données de l’architecture mutualisée. Il contient deux tables, comptes et des campagnes, chacun réparti par `account_id`. Les zones ombrées représentent des partitions, chaque dont la couleur représente le nœud de travail qu’il contient. Vert sont stockés ensemble sur un nœud de travail et de bleu sur un autre. Notez comment une requête de jointure entre les comptes et les campagnes pourrait avoir toutes les données nécessaires ensemble sur un seul nœud si vous limitez les deux tables au même compte\_id.

![colocalisation de l’architecture mutualisée](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Pour appliquer cette conception dans votre propre schéma, identifiez ce qui constitue un locataire dans votre application. Instances courantes incluent société, un compte, organisation ou client. Le nom de colonne est semblable à `company_id` ou `customer_id`. Examinez chacune de vos requêtes et vous vous demandez : elle fonctionnerait si elle avait des clauses WHERE supplémentaires pour restreindre toutes les tables impliquées aux lignes avec le même ID de client ?
Requêtes dans le modèle d’architecture mutualisé sont limités à un locataire, par exemple seraient porter les requêtes sur les ventes ou d’inventaire dans un magasin de certains.

#### <a name="best-practices"></a>Bonnes pratiques

-   **Partition distribuée tables par un client commun\_colonne id.** Par exemple, dans une application SaaS où les clients sont les entreprises, le locataire\_id seront probablement les entreprise\_id.
-   **Convertir les tables de petite taille entre locataires aux tables de référence.** Lorsque plusieurs locataires partagent une petite table d’informations, la distribuer comme une table de référence.
-   **Limiter filtre interroge toutes les applications par le locataire\_id.** Chaque requête doit demander des informations pour un seul client à la fois.

Lire le [mutualisée didacticiel](./tutorial-design-database-hyperscale-multi-tenant.md) pour obtenir un exemple de la création de ce type d’application.

### <a name="real-time-apps"></a>Applications en temps réel

L’architecture mutualisée présente une structure hiérarchique et utilise la colocalisation de données au routage des requêtes par client. En revanche, les architectures en temps réel dépendent des propriétés de distribution spécifiques de leurs données à obtenir un traitement hautement parallèle.

Nous utilisons « ID d’entité » en tant que terme pour les colonnes de distribution dans le modèle en temps réel. Entités standard sont des utilisateurs, d’hôtes ou d’appareils.

Requêtes en temps réel demandent en général des agrégats numériques regroupées par date ou par catégorie. Hyperscale envoie ces requêtes à chaque partition pour les résultats partiels et assemble la réponse finale sur le nœud coordinateur. Requêtes s’exécutent plus rapidement lorsque comme nombre de nœuds contribue que possible, et lorsque aucun nœud unique ne doit faire une quantité disproportionnée de travail.

#### <a name="best-practices"></a>Bonnes pratiques

-   **Choisissez une colonne présentant une cardinalité élevée en tant que la colonne de distribution.** Pour la comparaison, un \"état\" champ sur une table de commandes avec les valeurs « nouveau », « payante » et « livrée » est un choix médiocre de la colonne de distribution. Il part du principe que ces seules quelques valeurs, ce qui limite le nombre de partitions qui peut stocker les données et le nombre de nœuds qui peut le traiter. Entre les colonnes présentant une cardinalité élevée, il est judicieux de plus à choisir celles qui sont fréquemment utilisés dans les clauses group by ou en tant que clés de jointure.
-   **Choisissez une colonne avec distribution uniforme.** Si vous distribuez une table sur une colonne déviée vers certaines valeurs courantes, les données de la table seront ont tendance à accumuler dans certaines partitions. Les nœuds contenant ces partitions finira de travailler plus que les autres nœuds.
-   **Distribuer des tables de faits et de dimension sur leurs colonnes communes.**
    Votre table de faits peut avoir qu’une seule clé de distribution. Tables de jointure sur une autre clé ne sera pas au même emplacement que la table de faits. Choisissez une dimension colocaliser selon la taille des lignes de jointure et de la fréquence à laquelle il est joint.
-   **Modifier des tables de dimension dans les tables de référence.** Si une table de dimension ne peut pas être au même emplacement que la table de faits, vous pouvez améliorer les performances des requêtes en distribuant des copies de la table de dimension à tous les nœuds sous la forme d’une table de référence.

Lire le [didacticiel du tableau de bord en temps réel](./tutorial-design-database-hyperscale-realtime.md) pour obtenir un exemple de la création de ce type d’application.

### <a name="timeseries-data"></a>Données de TimeSeries

Dans une charge de travail de séries chronologiques, applications récentes informations lors de l’archivage d’anciennes informations de requête.

L’erreur la plus fréquente dans modélisez des informations de timeseries dans Hyperscale est à l’aide de l’horodatage en lui-même comme une colonne de distribution. Une distribution de hachage basée sur l’heure distribuera fois apparemment aléatoire dans différentes partitions plutôt que de conserver les plages de temps ensemble dans des partitions. Les requêtes impliquant des données temporelles généralement référencent des plages de temps (par exemple données les plus récentes), par conséquent, telle une distribution par hachage conduirait à réseau surcharge.

#### <a name="best-practices"></a>Bonnes pratiques

-   **Ne choisissez pas un horodatage comme colonne de distribution.** Choisissez une colonne de distribution différente. Dans une application mutualisée, utilisez l’ID de client ou dans une application en temps réel l’ID d’entité.
-   **Utilisez PostgreSQL partitionnement de table de temps à la place.** Utiliser le partitionnement de table pour diviser une table volumineuse de données chronologique en plusieurs tables héritées avec chaque contenant différentes périodes.  Distribution d’une table partitionnée Postgres dans Hyperscale crée des partitions pour les tables héritées.

Lire le [timeseries didacticiel](https://aka.ms/hyperscale-tutorial-timeseries) pour obtenir un exemple de la création de ce type d’application.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [colocalisation](concepts-hyperscale-colocation.md) entre les requêtes s’exécutent rapidement de données distribué aide
