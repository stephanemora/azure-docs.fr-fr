---
title: Comment Azure Cosmos DB permet une distribution mondiale clé en main | Microsoft Docs
description: Découvrez plus d’informations sur la géoréplication à l’échelle de la planète, le multimaître, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure Cosmos DB, service de base de données multimodèle distribué mondialement.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 1aa0fa3d4d9e1821a6980d9334456a78eba7bfbc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956912"
---
# <a name="how-azure-cosmos-db-enables-turnkey-global-distribution"></a>Comment Azure Cosmos DB permet une distribution mondiale clé en main
Azure Cosmos DB fournit les fonctionnalités suivantes pour vous permettre d’écrire facilement des applications distribuées mondialement. Ces fonctionnalités sont disponibles par l’intermédiaire des [API REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) basées sur le fournisseur de ressources d’Azure Cosmos DB, ainsi que sur le portail Azure.

## <a id="GlobalDistribution"></a>Distribution mondiale

### <a id="RegionalPresence"></a>Omniprésence régionale 
Azure étend constamment sa présence géographique en ajoutant de [nouvelles régions](https://azure.microsoft.com/regions/) en ligne. Classé comme *service fondamental* dans Azure, Azure Cosmos DB est disponible dans toutes les nouvelles régions Azure par défaut. Cela vous permet d’associer une région géographique à votre compte de base de données Azure Cosmos DB dès qu’Azure ouvre une nouvelle région aux entreprises.

### <a id="MultiMasterSupport"></a>Prise en charge du multimaître
Azure Cosmos DB fournit la prise en charge native côté serveur de plusieurs régions maîtres qui participent de façon égale à un modèle d’écriture universel. Cette prise en charge fournit une latence d’écriture inférieure à 10 ms et une disponibilité d’écriture de 99,999 % grâce à des [contrats de niveau de service soutenus financièrement](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Le multimaître est disponible pour toutes les API, notamment [SQL](sql-api-introduction.md), [MongoDB](mongodb-introduction.md), [Cassandra](cassandra-introduction.md), [Graph](graph-introduction.md) et [Table](table-introduction.md), et dans tous les langages de SDK pour Cosmos DB. Azure Cosmos DB prend en charge 4 niveaux de cohérence différents (obsolescence limitée, session, préfixe cohérent et cohérence à terme) pour les comptes dotés de la fonctionnalité de multimaître.

### <a id="UnlimitedRegionsPerAccount"></a>Associer un nombre illimité de régions à votre compte de base de données Azure Cosmos DB
Azure Cosmos DB vous permet d’associer n’importe quel nombre de régions Azure à votre compte de base de données Azure Cosmos DB. En dehors des restrictions de délimitations géographiques (par exemple, pour la Chine et l’Allemagne), il n’existe aucune restriction quant au nombre de régions qui peuvent être associées à votre compte de base de données Azure Cosmos DB. La figure suivante illustre un compte de base de données configuré pour couvrir 25 régions Azure :

![Compte de base de données Azure Cosmos DB couvrant 25 régions Azure](./media/distribute-data-globally-turnkey/spanning-regions.png)


### <a id="PolicyBasedGeoFencing"></a>Délimitations géographiques basées sur des stratégies
Azure Cosmos DB est conçu pour prendre en charge la délimitation géographique basée sur des stratégies. La délimitation géographique est un composant important pour garantir la gouvernance des données et les restrictions de conformité, des données, et peut empêcher l’association d’une région spécifique avec votre compte. Les exemples de délimitation géographique incluent (mais sans s’y limiter) l’étendue de la distribution mondiale aux régions dans un cloud souverain (par exemple, la Chine et l’Allemagne) ou dans les limites d’imposition du gouvernement (par exemple, l’Australie). Les stratégies sont contrôlées à l’aide des métadonnées de votre abonnement Azure.

### <a id="DynamicallyAddRegions"></a>Ajouter et supprimer des régions dynamiquement
Azure Cosmos DB vous permet d’ajouter (associer) ou de supprimer (dissocier) des régions de votre compte de base de données à tout moment (voir la [figure précédente](#UnlimitedRegionsPerAccount)). Grâce à la réplication parallèle des données sur plusieurs partitions, Azure Cosmos DB garantit que quand une nouvelle région est ajoutée, elle est disponible pour les opérations dans les 30 minutes n’importe où dans le monde (en supposant un volume de données de 100 To ou moins). 

### <a id="FailoverPriorities"></a>Priorités de basculement
Quand ils n’utilisent pas le multimaître, les clients peuvent contrôler l’ordre exact des basculements régionaux en cas de panne. Azure Cosmos DB vous permet d’associer une *priorité* à différentes régions associées au compte de la base de données (voir la figure ci-dessous). Azure Cosmos DB garantit que la séquence de basculement automatique a lieu dans l’ordre de priorité que vous avez spécifié. Pour plus d’informations sur les basculements régionaux, consultez [Basculements régionaux automatiques pour la continuité des activités dans Azure Cosmos DB](regional-failover.md). L’image suivante montre comment un locataire dans Azure Cosmos DB peut configurer l’ordre de priorité de basculement (volet droit) pour les régions associées à un compte de base de données :

![Configuration des priorités de basculement avec Azure Cosmos DB](./media/distribute-data-globally-turnkey/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Plusieurs modèles de cohérence bien définis pour les bases de données mondialement distribuées
Azure Cosmos DB prend en charge [plusieurs modèles de cohérence pratiques, intuitifs et bien définis](consistency-levels.md) garantis par des contrats SLA. Vous pouvez choisir un modèle de cohérence spécifique (à partir de la liste des options disponibles) en fonction des charges de travail/scénarios.

### <a id="TunableConsistency"></a>Cohérence ajustable pour les bases de données répliquées mondialement
Azure Cosmos DB vous permet de remplacer par programmation et d’assouplir le choix de cohérence par défaut à la demande, au moment de l’exécution.

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Régions de lecture et d’écriture configurables de manière dynamique
Azure Cosmos DB vous permet de configurer les régions (associées à la base de données) en « lecture », en « écriture » ou en « lecture/écriture ».

### <a id="ElasticallyScaleThroughput"></a>Mise à l’échelle flexible du débit à travers les régions Azure
Vous pouvez mettre à l’échelle un conteneur Azure Cosmos DB de manière flexible en provisionnant le débit par programmation. Le débit est appliqué à toutes les régions dans lesquelles le conteneur Azure Cosmos DB est distribué.

### <a id="GeoLocalReadsAndWrites"></a>Lectures et écritures géo-locales
Le principal avantage d’une base de données mondialement distribuée est qu’elle propose un accès aux données ne présentant qu’une faible latence n’importe où dans le monde. Azure Cosmos DB permet des opérations de lecture et d’écriture à faible latence (inférieure à 10 millisecondes) dans plus de 99 % des cas dans le monde entier. La solution garantit que toutes les lectures sont servies à partir de la région (locale) la plus proche. Pour répondre à une demande de lecture, le quorum se localise dans la région dans laquelle la lecture est émise et utilisée. Il en va de même pour les écritures. Une écriture est reconnue uniquement après qu’une majorité de réplicas a durablement validé l’écriture localement, mais sans être contrôlés sur des réplicas à distance pour reconnaître les écritures. En d’autres termes, le protocole de réplication d’Azure Cosmos DB fonctionne en partant du principe que les quorums en lecture et en écriture sont toujours locaux pour la région dans laquelle la demande a été émise.

### <a id="ManualFailover"></a>Basculement manuel
Azure Cosmos DB vous permet de déclencher le basculement d’un compte de base de données pour valider les propriétés de disponibilité de l’application entière de *bout en bout* (au-delà de la base de données). Étant donné que les propriétés de sécurité et d’activité de la détection de défaillances et du choix de responsable sont garanties, Azure Cosmos DB garantit *l’absence de perte de données* pendant les opérations de basculement manuelles initiées par les locataires.

### <a id="AutomaticFailover"></a>Basculement automatique
Azure Cosmos DB prend en charge le basculement automatique dans le cas d’une ou de plusieurs pannes régionales. Lors d’un basculement régional, Azure Cosmos DB gère la latence de lecture, la disponibilité, la cohérence et les contrats SLA de débit. Azure Cosmos DB fournit une limite supérieure pour le temps d’achèvement d’une opération de basculement automatique. Il s’agit de la fenêtre de perte de données potentielle pendant une panne régionale.

### <a id="GranularFailover"></a>Conçu pour différentes granularités de basculement
Actuellement, les fonctionnalités de basculement automatiques et manuelles sont exposées au niveau de granularité du compte de base de données. Notez que, en interne, Azure Cosmos DB est conçu pour offrir un basculement *automatique* au niveau de granularité le plus fin d’une base de données, d’un conteneur, voire d’une partition (d’un conteneur qui possède un éventail de clés). 

### <a id="MultiHomingAPIs"></a>Multihébergement dans Azure Cosmos DB

Dans un scénario monomaître où vous disposez d’une région d’écriture et de plusieurs réplicas de lecture, Azure Cosmos DB vous permet d’interagir avec une base de données à l’aide de points de terminaison logiques (indépendants des régions) ou physiques (spécifiques à une région). L’utilisation de points de terminaison logiques garantit que l’application peut en toute transparence être multihébergée en cas de basculement. Un point de terminaison physique, quant à lui, fournit un contrôle précis à l’application pour rediriger les lectures et écritures vers des régions spécifiques. Dans les scénarios avec plusieurs régions activées pour l’écriture, les utilisateurs doivent spécifier des points de terminaison spécifiques aux régions, qui sont ajoutées par ordre de priorité pour l’utilisation et les redirections.

Vous trouverez plus d’informations sur la configuration des préférences de lecture pour [l’API SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), [l’API Table](../cosmos-db/tutorial-global-distribution-table.md) et [l’API MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) dans ces articles.

### <a id="TransparentSchemaMigration"></a>Schéma de base de données et migration d’index transparents et cohérents 
Azure Cosmos DB est entièrement [sans schéma](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). La conception unique du moteur de base de données permet à Azure Cosmos DB d’indexer automatiquement et de manière synchrone toutes les données dès l’ingestion, sans demander à l’utilisateur de fournir un schéma ou des index secondaires. Cela vous permet d’itérer votre application mondialement distribuée rapidement et sans soucis liés au schéma de base de données et à la migration d’index de base de données ou à la coordination des déploiements d’application en plusieurs phases de modifications de schéma. Azure Cosmos DB garantit que les modifications apportées à l’indexation de stratégies explicitement faites par vous n’entraînent pas de dégradation des performances ou de la disponibilité.  

### <a id="ComprehensiveSLAs"></a>Contrats de niveau de service complets (au-delà de la haute disponibilité)
En tant que service de base de données mondialement distribué, Azure Cosmos DB propose des contrats SLA bien définis et complets pour la **disponibilité**, la **latence**, le **débit** et la **cohérence** de la base de données exécutée à l’échelle mondiale, quel que soit le nombre de régions associées à celle-ci.  

## <a id="LatencyGuarantees"></a>Garanties de latence
Le principal avantage d’un service de base de données mondialement distribué comme Azure Cosmos DB est de proposer un accès à vos données ne présentant qu’une faible latence, n’importe où dans le monde. Azure Cosmos DB offre une latence faible garantie à 99 % pour diverses opérations de base de données. Le protocole de réplication qu’Azure Cosmos DB emploie garantit que les opérations de base de données (lectures et écritures) sont toujours effectuées dans la région locale du client. Le contrat SLA pour la latence que fournit Azure Cosmos DB offre une garantie à hauteur de 99 % pour les lectures, les écritures indexées (de manière synchrone) et les requêtes pour différentes tailles de demande et de réponse. Les garanties de latence pour les écritures incluent les validations de quorum majoritaire durables dans la région locale.

### <a id="LatencyAndConsistency"></a>Relation de latence avec la cohérence 
Pour qu’un service mondialement distribué offre une cohérence forte dans une configuration mondialement distribuée, il doit effectuer une réplication synchrone des écritures ou effectuer des lectures interrégionales de façon synchrone. La vitesse de la lumière et la fiabilité du réseau étendu déterminent le fait qu’une cohérence forte entraîne des latences élevées et une disponibilité réduite des opérations de base de données. Ainsi, pour garantir des temps de latence faibles à 99 % et une disponibilité de 99,99 % sur tous les comptes à région unique et tous les comptes à plusieurs régions avec cohérence souple, ainsi qu’une disponibilité de 99,999 % sur tous les comptes de base de données à plusieurs régions, le service doit utiliser la réplication asynchrone. Cela nécessite que le service offre également des [modèles de cohérence bien définis et flexibles](consistency-levels.md) – plus faibles que puissants (pour offrir des garanties de latence faible et de disponibilité) et, dans l’idéal, plus puissants que la cohérence « finale » (avec un modèle de programmation intuitif).

Azure Cosmos DB garantit qu’une opération de lecture n’est pas requise pour contacter des réplicas dans plusieurs régions, afin de fournir une garantie de niveau de cohérence spécifique. De même, la solution garantit qu’une opération d’écriture ne reste pas bloquée pendant que les données sont répliquées dans toutes les régions (autrement dit, les écritures sont répliquées de manière asynchrone dans les différentes régions). Dans le cas de comptes de base de données multirégions, plusieurs niveaux de cohérence forts et flexibles sont disponibles. 

### <a id="LatencyAndAvailability"></a>Relation de la latence avec la disponibilité 
La latence et la disponibilité sont les deux faces d’une même pièce. Dans un état stable, nous parlons de latence d’une opération et, en présence de défaillances ou de partitions réseau, nous parlons de disponibilité. Du point de vue de l’application, une opération de base de données lente ne peut pas être différenciée d’une base de données qui n’est pas disponible. 

Pour distinguer une latence élevée de l’indisponibilité, Azure Cosmos DB fournit une limite supérieure absolue pour la latence de diverses opérations de base de données. Si l’opération de base de données prend plus de temps que la limite supérieure pour se terminer, Azure Cosmos DB renvoie une erreur de délai d’expiration. Le contrat SLA de disponibilité Azure Cosmos DB garantit que les délais d’expiration sont imputés au contrat SLA de disponibilité. 

### <a id="LatencyAndThroughput"></a>Relation de latence avec le débit
Azure Cosmos DB ne vous fait pas choisir entre la latence et le débit. La solution respecte le contrat de niveau de service pour la latence à 99 % et vous fournit le débit que vous avez provisionné. 

## <a id="ConsistencyGuarantees"></a>Garanties de cohérence
Même si le [modèle de cohérence forte](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) constitue la référence en matière de programmabilité des données, celui-ci entraîne une latence élevée (dans un état stable) et une perte de disponibilité (en cas de défaillance). 

Azure Cosmos DB vous offre un modèle de programmation bien défini vous permettant d’analyser la cohérence des données répliquées. Afin de vous permettre de créer facilement des applications distribuées mondialement avec des fonctionnalités de multihébergement, les modèles de cohérence exposés par Azure Cosmos DB sont conçus pour être indépendants des régions en général, et indépendants de la région où les lectures et les écritures sont délivrées. 

Le contrat SLA de cohérence d’Azure Cosmos DB garantit que 100 % des demandes de lecture respectent la garantie de cohérence pour le modèle de cohérence que vous avez spécifié (au niveau du compte de base de données ou de la demande). Une demande de lecture est considérée comme respectant le contrat de niveau de service de cohérence si toutes les garanties de cohérence associées au niveau de cohérence sont satisfaites. Le tableau suivant répertorie le modèle de cohérence et les garanties de cohérence. Chacun de ces modèles de cohérence garantit un contrat SLA de 100 %. 

|Modèle de cohérence  | Caractéristiques  |
|---------|---------|
|Remarque |  Linéarisable  |
|Bounded staleness (En fonction de l'obsolescence)  |  Lecture unitone (au sein d’une région), Préfixe cohérent, Obsolescence limitée &lt; K,T   |
|session  |  Lecture de votre propre écriture, Lecture unitone, Préfixe cohérent    |
|Préfixe cohérent  | Préfixe cohérent  |

### <a id="ConsistencyAndAvailability"></a>Relation de la cohérence avec la disponibilité
Le [résultat d’impossibilité](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) du [théorème CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prouve qu’il est en effet impossible qu’un système reste disponible et offre une cohérence linéarisable en cas de défaillance. Les services de base de données doivent choisir d’être CP ou AP ; les systèmes CP renoncent à la disponibilité en faveur d’une cohérence linéarisable, tandis que les systèmes AP renoncent à la [cohérence linéarisable](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) en faveur de la disponibilité. Azure Cosmos DB n’enfreint jamais le modèle de cohérence demandé, ce qui en fait un système formellement CP. Toutefois, dans la pratique, la cohérence n’est pas une proposition à prendre ou à laisser ; il existe plusieurs modèles de cohérence bien définis dans le spectre de cohérence entre cohérence linéarisable et cohérence finale. Dans Azure Cosmos DB, nous avons identifié plusieurs modèles de cohérence souples qui sont applicables à des scénarios pratiques et utilisables de façon intuitive. Azure Cosmos DB vous évite de faire des compromis en matière de cohérence et de disponibilité en proposant [plusieurs modèles de cohérence souples bien définis](consistency-levels.md), ainsi qu’une disponibilité de 99,99 % sur tous les comptes de base de données à région unique et une disponibilité de lecture et d’écriture de 99,999 % sur tous les comptes de base de données à plusieurs régions. 

### <a id="ConsistencyAndAvailability"></a>Relation de la cohérence avec la latence
Une variation plus complète du théorème CAP est appelée [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), qui constitue également un compromis en termes de latence et de cohérence dans un état stable. Elle établit que, dans un état stable, un système de base de données doit choisir entre la cohérence et la latence. Avec plusieurs modèles de cohérence assouplis (pris en charge par la réplication asynchrone et les quorums de lecture et d’écriture locaux), Azure Cosmos DB garantit que toutes les lectures et écritures sont effectuées dans les régions de lecture et d’écriture locales, respectivement. Ainsi, Azure Cosmos DB offre des garanties de faible latence dans la région pour les modèles de cohérence donnés.  

### <a id="ConsistencyAndThroughput"></a>Relation de la cohérence avec le débit
Étant donné que l’implémentation d’un modèle de cohérence spécifique varie selon le choix d’un [type de quorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), le débit varie également selon le choix d’un modèle de cohérence. Par exemple, dans Azure Cosmos DB, la charge d’unités de requête (RU) pour les lectures fortement cohérentes représente environ le *double* de celle des lectures cohérentes. Dans ce cas, vous devez provisionner le double de RU pour obtenir le même débit. L’image suivante montre la relation de la capacité de lecture pour un modèle de cohérence spécifique dans Azure Cosmos DB :

![Relation entre cohérence et débit](./media/distribute-data-globally-turnkey/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garanties de débit 
Azure Cosmos DB vous permet, en toute flexibilité, de mettre à l’échelle le débit, ainsi que le stockage, dans un nombre quelconque de régions en fonction de la demande ou de vos besoins. L’image suivante montre un seul conteneur Azure Cosmos DB partitionné horizontalement (entre trois partitions de ressources dans une région), puis distribué mondialement entre trois régions Azure :

![Conteneurs distribués et partitionnés d’Azure Cosmos DB](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Un conteneur Azure Cosmos DB est distribué dans deux dimensions : (i) au sein d’une région et (ii) entre les régions. Voici comment procéder : 

* **Distribution locale** : dans une région unique, un conteneur Azure Cosmos DB est mis à l’échelle horizontalement en termes de *partitions de ressources*. Chaque partition de ressources gère un ensemble de clés et est fortement cohérente et hautement disponible, étant physiquement représentée par quatre réplicas également appelés *jeu de réplicas* et une réplication de machines d’état entre ces réplicas. Azure Cosmos DB est un système entièrement régi par les ressources, où une partition de ressources est chargée de fournir sa part du débit pour le budget de ressources système qui lui est alloué. La mise à l’échelle d’un conteneur Azure Cosmos DB est transparente pour les utilisateurs. Azure Cosmos DB gère les partitions de ressources, et les fractionne et fusionne à mesure qu’évoluent les exigences de stockage et de débit. 
* **Distribution mondiale** : s’il s’agit d’une base de données à plusieurs régions, chacune des partitions de ressources est distribuée entre ces régions. Les partitions de ressources possédant le même jeu de clés dans différentes régions forment le *jeu de partitions* (voir [figure précédente](#ThroughputGuarantees)).  Les partitions de ressources au sein d’un jeu de partitions sont coordonnées à l’aide de la réplication de machines d’état dans différentes régions associées à la base de données. Selon le niveau de cohérence configuré, les partitions de ressources au sein d’un jeu de partitions sont configurées dynamiquement à l’aide de différentes topologies (étoile, cascade, arborescence, etc.). 

Grâce à une gestion des partitions très réactive, à l’équilibrage de charge et à une gouvernance stricte des ressources, Azure Cosmos DB vous permet des mises à l’échelle flexibles dans plusieurs régions Azure associées à un conteneur ou à une base de données Azure Cosmos DB. La modification du débit provisionné est une opération d’exécution dans Azure Cosmos DB. Comme pour d’autres opérations de base de données, Azure Cosmos DB garantit la limite supérieure absolue de la latence pour votre demande de modification du débit provisionné. Par exemple, la figure suivante montre le conteneur d’un client dont le débit est provisionné en toute flexibilité (1 à 10 millions de demandes/s entre deux régions) en fonction de la demande.

![Débit configuré en toute flexibilité d’Azure Cosmos DB](./media/distribute-data-globally-turnkey/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relation du débit avec la cohérence 
Elle est identique à ce qui est décrit dans [Relation de la cohérence avec le débit](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relation du débit avec la disponibilité
Azure Cosmos DB maintient sa disponibilité élevée quand des modifications sont apportées au débit provisionné. Azure Cosmos DB gère de façon transparente les partitions de ressources (et effectue des opérations de fractionnement, de fusion et de clonage) et vérifie que les opérations n’altèrent pas les performances ou la disponibilité, tandis que l’application augmente ou diminue son débit avec flexibilité. 

## <a id="AvailabilityGuarantees"></a>Garanties de disponibilité
Azure Cosmos DB propose un contrat SLA avec une disponibilité à 99,99 % pour tous les comptes de base de données à région unique et à plusieurs régions avec cohérence souple, ainsi qu’une disponibilité à 99,999 % pour tous les comptes de base de données à plusieurs régions. Comme indiqué précédemment, les garanties de disponibilité d’Azure Cosmos DB incluent une limite supérieure absolue de la latence pour toutes les opérations de données et de plan de contrôle. Les garanties de disponibilité ne changent pas avec le nombre de régions ou la distance géographique entre les régions. Les garanties de disponibilité sont applicables quant aux basculements manuels et aux basculements automatiques. Azure Cosmos DB offre des API multihébergement transparentes qui garantissent que votre application fonctionne sur les points de terminaison logiques et achemine en toute transparence les demandes vers une nouvelle région en cas de basculement ou vous pouvez spécifier des points de terminaison régionaux dans l’ordre de priorité pour l’utilisation. Votre application ne doit pas être redéployée en cas de basculement régional, et les contrats de niveau de service de disponibilité sont conservés à tout moment.

### <a id="AvailabilityAndConsistency"></a>Relation de la disponibilité avec la cohérence, la latence et le débit
La relation de la disponibilité avec la cohérence, la latence et le débit est décrite dans les sections [Relation de la cohérence avec la disponibilité](#ConsistencyAndAvailability), [Relation de la latence avec la disponibilité](#LatencyAndAvailability) et [Relation du débit avec la disponibilité](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Mesures de contrat de niveau de service orientées client
Azure Cosmos DB expose de façon transparente les métriques de débit, de latence, de cohérence et de disponibilité. Ces métriques sont accessibles par programmation et dans le portail Azure (voir la figure ci-dessous). Vous pouvez également définir des alertes sur différents seuils à l’aide d’Azure Application Insights. L’image suivante montre des métriques de cohérence, de latence, de débit et de disponibilité qui sont disponibles en toute transparence pour chaque locataire :
 
![Mesures du contrat SLA visible par le client d’Azure Cosmos DB](./media/distribute-data-globally-turnkey/customer-slas.png)

## <a id="Next Steps"></a>Étapes suivantes

* [Principaux avantages de la distribution mondiale d’Azure Cosmos DB](distribute-data-globally-benefits.md)

* [Guide pratique pour configurer la réplication de base de données mondiale dans Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Guide pratique pour activer le multimaître pour les comptes Azure Cosmos DB](enable-multi-master.md)

* [Fonctionnement du basculement manuel et automatique dans Azure Cosmos DB](regional-failover.md)

* [Présentation de la résolution des conflits dans Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Utilisation du multimaître avec des bases de données NoSQL open source](multi-master-oss-nosql.md)


## <a id="References"></a>Références
1. Eric Brewer. [Towards Robust Distributed Systems](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Vers des systèmes distribués robustes)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP douze ans plus tard : en quoi les règles ont-elles changé ?)
3. Gilbert, Lynch. - [Brewer&amp;#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjecture et faisabilité de services web cohérents, disponibles et tolérant le partitionnement)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Compromis en termes de cohérence dans la conception des systèmes de base de données distribués modernes)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Arrêtez d’appeler les bases de données CP ou AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Probabilités en fonction de l’obsolescence limitée (PBS) pour les quorums partiels pratiques)
7. Naor and Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Charge, capacité et disponibilité dans les systèmes de quorum)
8. Herlihy and Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linéarisabilité : Une condition d’exactitude pour les objets simultanés)
9. [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
