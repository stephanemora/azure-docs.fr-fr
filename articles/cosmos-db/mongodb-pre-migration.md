---
title: Étapes de prémigration pour la migrations de données vers l’API Azure Cosmos DB pour MongoDB
description: Ce document fournit une vue d’ensemble des prérequis pour une migration de données de MongoDB vers Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 05/17/2021
ms.author: anfeldma
ms.openlocfilehash: bb62219b8579fb0e87011ccfcae04b28aa7f0181
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471103"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Étapes de prémigration pour les migrations de données de MongoDB vers l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Veuillez lire ce guide dans son intégralité avant de suivre les étapes de prémigration.
>

Ce guide de prémigration de MongoDB fait partie d'une série consacrée à la migration de MongoDB. Les principales étapes de migration de MongoDB sont la prémigration, la migration proprement dite et la [postmigration](mongodb-post-migration.md), comme indiqué ci-dessous.

![Schéma des étapes de migration.](./media/mongodb-pre-migration/overall-migration-steps.png)

## <a name="overview-of-pre-migration"></a>Présentation de la prémigration

Il est essentiel de procéder à une planification et de prendre certaines décisions avant d'entamer la migration réelle des données. Ce processus décisionnel initial est appelé « prémigration ». L'objectif de la prémigration est (1) de vous assurer que vous avez configuré Azure Cosmos DB pour répondre aux besoins de votre application après la migration et (2) de planifier la façon dont vous allez exécuter la migration.

Suivez les étapes ci-dessous pour effectuer une prémigration complète :
* [Découvrez vos ressources MongoDB existantes et créez un artefact pour assurer leur suivi](#pre-migration-discovery)
* [Évaluez la disponibilité de vos ressources MongoDB existantes pour la migration des données](#pre-migration-assessment)
* [Mappez vos ressources MongoDB existantes avec les nouvelles ressources Azure Cosmos DB](#pre-migration-mapping)
* [Planifiez la logistique du processus de migration de bout en bout avant de lancer la migration des données à grande échelle](#execution-logistics)

Exécutez ensuite votre migration conformément à votre plan de prémigration.

Enfin, suivez les [étapes critiques de postmigration que sont le basculement et l'optimisation](mongodb-post-migration.md).

Toutes les étapes ci-dessus sont essentielles à la réussite de la migration.

Lorsque vous planifiez une migration, nous vous recommandons, dans la mesure du possible, de procéder à une planification au niveau de chaque ressource.

## <a name="pre-migration-discovery"></a>Découverte de la prémigration

La première étape de la prémigration est la découverte des ressources. Au cours de cette étape, vous allez essayer de dresser une liste exhaustive des ressources existantes dans votre patrimoine de données MongoDB.

### <a name="create-a-data-estate-migration-spreadsheet"></a>Créer une feuille de calcul pour la migration du patrimoine de données

Créez une **feuille de calcul** de migration du patrimoine de données que vous utiliserez pour le suivi de votre migration, avec votre logiciel de productivité préféré. 
   * L'objectif de cette feuille de calcul est d'améliorer votre productivité et de planifier la migration de bout en bout.
   * Le choix de la structure de la feuille de calcul vous appartient. Les points suivants fournissent quelques recommandations.
   * Cette feuille de calcul doit être structurée pour répertorier les ressources de votre patrimoine de données.
   * Chaque ligne de la liste correspond à une ressource (base de données ou collection).
   * Chaque colonne correspond à une propriété de la ressource ; pour le moment, vous devez au moins disposer d'une colonne *Nom* et d'une colonne *Taille des données (Go)* . Idéalement, vous pouvez aussi collecter des informations sur la version de MongoDB pour chaque ressource, auquel cas vous devez ajouter une colonne *Version de Mongo*. 
   * Dans un premier temps, vous allez compléter cette feuille de calcul avec la liste des ressources existantes de votre patrimoine de données MongoDB. Au fil de votre progression dans ce guide, vous transformerez cette feuille de calcul en document de suivi pour la planification de votre migration de bout en bout, en y ajoutant des colonnes en fonction des besoins.

### <a name="discover-existing-mongodb-data-estate-resources"></a>Découvrir les ressources du patrimoine de données MongoDB existant

À l'aide d'un outil de découverte approprié, identifiez les ressources (bases de données, collections) de votre patrimoine de données MongoDB existant, de la manière la plus exhaustive possible. 

Voici quelques outils que vous pouvez utiliser pour découvrir les ressources :
   * [Interpréteur de commandes MongoDB](https://www.mongodb.com/try/download/shell)
   * [Boussole MongoDB](https://www.mongodb.com/try/download/compass)

## <a name="pre-migration-assessment"></a>Évaluation de la prémigration

Deuxièmement, comme préambule à la planification de votre migration, évaluez l'état de préparation de chaque ressource de votre patrimoine de données. 

La version de MongoDB est le principal facteur qui influe sur l'état de préparation. Azure Cosmos DB prend actuellement en charge les versions 3.2, 3.6 et 4.0 du protocole binaire de MongoDB. Nous espérons que vous disposez dans votre feuille de calcul de planification de la migration d'une colonne pour la *version de MongoDB*. Parcourez votre feuille de calcul et mettez en surbrillance toutes les ressources qui utilisent des versions de MongoDB incompatibles avec Azure Cosmos DB.

## <a name="pre-migration-mapping"></a>Mappage de prémigration

Une fois les étapes de découverte et d'évaluation terminées, vous en avez fini avec le côté MongoDB de l'équation. Il est maintenant temps de planifier le côté Azure Cosmos DB. Comment allez-vous installer et configurer vos ressources Azure Cosmos DB de production ? Effectuez votre planification au niveau de *chaque ressource*. Pour ce faire, vous devez ajouter les colonnes suivantes à votre feuille de calcul de planification : 
* Mappage d'Azure Cosmos DB 
* Clé de partition 
* Modèle de données
* Débit dédié et débit partagé

De plus amples détails sont fournis dans les sections qui suivent.

### <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Considérations relatives à l’utilisation de l’API Azure Cosmos DB pour MongoDB

Avant de planifier votre patrimoine de données Azure Cosmos DB, assurez-vous de bien comprendre les concepts Azure Cosmos DB suivants :

- **Modèle de capacité** : La capacité de base de données dans Azure Cosmos DB est basée sur un modèle basé sur le débit. Ce modèle est basé sur les [unités de requête par seconde](request-units.md) et constitue une unité qui représente le nombre d’opérations de base de données pouvant être exécutées par seconde sur une collection. Cette capacité peut être allouée [au niveau d’une collection ou d’une base de données](set-throughput.md), et elle peut être approvisionnée sur un modèle d’allocation ou à l’aide du [débit approvisionné en mode de mise à l’échelle automatique](provision-throughput-autoscale.md).

- **Unités de requête** : Chaque opération de base de données a un coût d’unités de requête (RU) associé dans Azure Cosmos DB. Une fois l’opération exécutée, ce coût est soustrait du niveau d’unités de requête disponible à une seconde donnée. Si une demande nécessite plus d’unités de requête que le nombre d’unités de requête actuellement allouées/s, il existe deux options pour résoudre le problème : augmenter le nombre d’unités de requête ou attendre que la seconde suivante démarre, puis recommencer l’opération.

- **Capacité élastique** : La capacité d’une collection ou d’une base de données donnée peut changer à tout moment. Cela permet à la base de données de s’adapter de manière élastique aux exigences de débit de votre charge de travail.

- **Partitionnement automatique** : Azure Cosmos DB fournit un système de partitionnement automatique qui requiert un seul partitionnement (ou une clé de partition). Le [mécanisme de partitionnement automatique](partitioning-overview.md) est partagé entre toutes les API Azure Cosmos DB et permet une mise à l’échelle transparente des données et du débit via une distribution horizontale.

### <a name="plan-the-azure-cosmos-db-data-estate"></a>Planifier le patrimoine de données Azure Cosmos DB

Déterminez les ressources Azure Cosmos DB que vous allez créer. Pour cela, vous devez passer en revue la feuille de calcul de migration de votre patrimoine de données et mapper chaque ressource MongoDB existante avec une nouvelle ressource Azure Cosmos DB. 
* Prévoyez que chaque base de données MongoDB deviendra une base de données Azure Cosmos DB.
* Prévoyez que chaque collection MongoDB deviendra une collection Azure Cosmos DB.
* Choisissez une convention d'affectation de noms pour vos ressources Azure Cosmos DB. En l'absence de changements dans la structure des bases de données et des collections, il est généralement préférable de conserver les mêmes noms de ressources.
* Dans MongoDB, le partitionnement des collections est facultatif. Dans Azure Cosmos DB, chaque collection est partitionnée.
* *Ne partez pas du principe que la clé de partition de votre collection MongoDB devient la clé de partition de votre collection Azure Cosmos DB. Et ne partez pas du principe que votre modèle de données/structure de document MongoDB existant est celui que vous utiliserez sur Azure Cosmos DB.* 
   * La clé de partition est le paramètre le plus important pour optimiser la scalabilité et les performances d'Azure Cosmos DB, devant le paramètre de modélisation des données. Ces deux paramètres sont immuables et ne peuvent pas être modifiés une fois qu'ils sont définis ; par conséquent, il est très important de les optimiser au cours de la phase de planification. Pour plus d'informations, suivez les instructions de la section [Décisions immuables](#immutable-decisions).
* Azure Cosmos DB ne reconnaît pas certains types de collections MongoDB, comme les collections limitées. Pour ces ressources, contentez-vous de créer des collections Azure Cosmos DB normales.
* Azure Cosmos DB dispose de deux types de collections qui lui sont propres : le débit partagé et le débit dédié. Débit partagé ou débit dédié ? Il s'agit là d'une autre décision importante et immuable qu'il est essentiel de prendre lors de la phase de planification. Pour plus d'informations, suivez les instructions de la section [Décisions immuables](#immutable-decisions).

### <a name="immutable-decisions"></a>Décisions immuables

Les choix suivants en matière de configuration d'Azure Cosmos DB ne peuvent pas être modifiés ou annulés une fois que vous avez créé une ressource Azure Cosmos DB. Il est donc important de bien les définir lors de la planification de la prémigration, avant de lancer la migration :
* Suivez [ce guide](partitioning-overview.md) pour choisir la meilleure clé de partition. Le partitionnement est un point clé à prendre en compte avant de migrer des données. Azure Cosmos DB utilise un partitionnement complètement managé pour augmenter la capacité d’une base de données à répondre aux exigences de stockage et de débit. Cette fonctionnalité n’a pas besoin de l’hébergement ni de la configuration des serveurs de routage.   
   * D’une façon similaire, la fonctionnalité de partitionnement ajoute automatiquement de la capacité et rééquilibre les données en conséquence. Pour plus d’informations et pour obtenir des suggestions concernant le choix de la clé de partition appropriée pour vos données, consultez l’article [Choix d’une de clé de partition](partitioning-overview.md#choose-partitionkey). 
* Suivez [ce guide](modeling-data.md) pour choisir un modèle de données.
* Suivez [ce guide](optimize-cost-throughput.md#optimize-by-provisioning-throughput-at-different-levels) afin de choisir entre débit dédié et débit partagé pour chacune des ressources que vous souhaitez migrer.
* Vous trouverez [ici](how-to-model-partition-example.md) un exemple de partitionnement et de modélisation des données qui vous aidera lors de votre processus de prise de décision.

### <a name="cost-of-ownership"></a>Coût total de possession

* Estimez le coût total de possession de vos nouvelles ressources Azure Cosmos DB à l'aide de la [calculatrice de capacité Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/).

### <a name="estimating-throughput"></a>Estimer le débit

* Dans Azure Cosmos DB, le débit est provisionné à l’avance et mesuré en unités de requête par seconde. Contrairement aux machines virtuelles ou aux serveurs locaux, il est facile d’effectuer un scale-up ou scale-down des unités de requête à tout moment. Vous pouvez instantanément modifier le nombre d’unités de requête provisionnées. Pour en savoir plus, voir [Unités de requête dans Azure Cosmos DB](request-units.md).

* Vous pouvez utiliser la [calculatrice de capacité Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) pour déterminer la quantité d’unités de requête en fonction de la configuration de votre compte de base de données, de la quantité de données, de la taille du document et des opérations de lecture et d’écriture requises par seconde.

* Les principaux facteurs qui affectent le nombre d’unités de requête nécessaires sont les suivants :
   * **Taille du document** : plus la taille d’un élément/document augmente, plus le nombre d’unités de requête consommées pour le lire ou l’écrire augmente.

   * **Nombre de propriétés de document** : le nombre d’unités de requête consommées pour créer ou mettre à jour un document est lié au nombre, à la complexité et à la longueur de ses propriétés. Vous pouvez réduire la consommation d’unités de requête pour les opérations d’écriture en [limitant le nombre de propriétés indexées](mongodb-indexing.md).

   * **Modèles de requête** : la complexité d’une requête a une incidence sur le nombre d’unités de requête qu’elle consomme. 

* La meilleure façon de comprendre le coût des requêtes consiste à utiliser des exemples de données dans Azure Cosmos DB et à [exécuter des exemples de requête dans l’interpréteur de commandes MongoDB](connect-mongodb-account.md) à l’aide de la commande `getLastRequestStastistics` pour obtenir les frais de requêtes, ce qui fournira le nombre d’unités de requête consommées :

    `db.runCommand({getLastRequestStatistics: 1})`

    Cette commande génère un document JSON semblable au suivant :

    ```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

* Vous pouvez également utiliser [les paramètres de diagnostic](cosmosdb-monitor-resource-logs.md) pour comprendre la fréquence et les modèles des requêtes exécutées sur Azure Cosmos DB. Les résultats des journaux de diagnostic peuvent être envoyés à un compte de stockage, à une instance EventHub ou à [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).  

## <a name="pre-migration-logistics-planning"></a>Planification de la logistique de prémigration

Enfin, maintenant que vous disposez d'une vision globale de votre patrimoine de données existant et d'une conception pour votre nouveau patrimoine de données Azure Cosmos DB, vous êtes prêt à planifier l'exécution de votre processus de migration de bout en bout. Encore une fois, effectuez votre planification au niveau de *chaque ressource*, en ajoutant des colonnes à votre feuille de calcul pour capturer les dimensions logistiques ci-dessous.

### <a name="execution-logistics"></a>Logistique d'exécution
* Attribuez la responsabilité de la migration de chaque ressource existante de MongoDB vers Azure Cosmos DB. Il vous incombe de décider comment vous allez tirer parti de vos équipes pour mener à bien votre migration. Pour les petites migrations, vous pouvez confier à une seule équipe le lancement de l'ensemble de la migration et le suivi de sa progression. Pour les migrations plus volumineuses, vous pouvez confier à certains membres de l'équipe la responsabilité de la migration et de la surveillance de telle ou telle ressource.
* Une fois que vous avez attribué la responsabilité de la migration de vos ressources, vous devez choisir le ou les outils de migration appropriés. Pour les petites migrations, vous pouvez utiliser un outil de migration tel qu'un outil natif MongoDB ou Azure DMS afin de migrer toutes vos ressources en une seule fois. Pour les migrations plus importantes ou avec des exigences particulières, vous pouvez choisir des outils de migration qui offre une granularité « par ressource ».
   * Avant de planifier les outils de migration à utiliser, nous vous recommandons de vous familiariser avec les options disponibles. Le service [Azure Database Migration Service pour l’API Azure Cosmos DB pour MongoDB](../dms/tutorial-mongodb-cosmos-db.md) simplifie la migration des données en fournissant une plateforme d’hébergement complètement managée, des options de surveillance de la migration et une gestion automatique de la limitation. La liste complète des options est la suivante :

   |**Type de migration**|**Solution**|**Considérations**|
   |---------|---------|---------|
   |En ligne|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Utilise la bibliothèque d’exécuteurs en bloc Azure Cosmos DB <br/>&bull; Adapté aux jeux de données volumineux et chargé de la réplication des modifications dynamiques <br/>&bull; Fonctionne uniquement avec d’autres sources MongoDB|
   |Hors connexion|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Utilise la bibliothèque d’exécuteurs en bloc Azure Cosmos DB <br/>&bull; Adapté aux jeux de données volumineux et chargé de la réplication des modifications dynamiques <br/>&bull; Fonctionne uniquement avec d’autres sources MongoDB|
   |Hors connexion|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).|&bull; Facile à configurer et prise en charge de plusieurs sources <br/>&bull; Utilise la bibliothèque d’exécuteurs en bloc Azure Cosmos DB <br/>&bull; Adapté aux jeux de données volumineux <br/>&bull; L’absence de points de contrôle signifie que tout problème au cours de la migration impose le redémarrage de l’ensemble du processus de migration<br/>&bull; L’absence de file d’attente de lettres mortes signifie que quelques fichiers erronés peuvent arrêter l’ensemble du processus de migration <br/>&bull; Nécessite du code personnalisé pour augmenter le débit de lecture pour certaines sources de données|
   |Hors connexion|[Outils Mongo existants (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Facilité de configuration et d’intégration <br/>&bull; Nécessite une gestion personnalisée des limitations|

   * Si votre ressource peut tolérer une migration hors connexion, utilisez le schéma ci-dessous pour choisir l'outil de migration approprié :

   ![Outils de migration hors connexion.](./media/mongodb-pre-migration/offline-tools.png)

   * Si votre ressource requiert une migration en ligne, utilisez le schéma ci-dessous pour choisir l'outil de migration approprié :

   ![Outils de migration en ligne.](./media/mongodb-pre-migration/online-tools.png)

* Une fois que vous avez choisi les outils de migration pour chaque ressource, l'étape suivante consiste à hiérarchiser les ressources que vous allez migrer. Une bonne hiérarchisation peut vous aider à respecter votre calendrier de migration. Une bonne pratique consiste à donner la priorité à la migration des ressources dont le déplacement prend le plus de temps ; la migration de ces ressources en premier sera plus efficace. En outre, étant donné que ces migrations chronophages impliquent généralement plus de données, elles sont souvent plus gourmandes en ressources pour l'outil de migration et sont donc plus susceptibles d'exposer rapidement les éventuels problèmes inhérents à votre pipeline de migration. Cela réduit les risques de retard de calendrier dus à des problèmes de pipeline de migration.
* Planifiez la façon dont vous souhaitez surveiller la progression de la migration une fois qu'elle aura commencé. Si vous coordonnez votre migration des données au sein d'une équipe, prévoyez un rythme régulier de synchronisations d'équipe afin d'avoir une vue d'ensemble de l'avancement des migrations prioritaires.

### <a name="supported-migration-scenarios"></a>Scénarios de migration pris en charge

Le meilleur choix d'outil de migration MongoDB dépend de votre scénario de migration. 

#### <a name="types-of-migrations"></a>Types de migrations

Les outils compatibles pour chaque scénario de migration sont présentés ci-dessous :

![Scénarios de migration pris en charge.](./media/mongodb-pre-migration/migration-tools-use-case-table.png)

#### <a name="tooling-support-for-mongodb-versions"></a>Prise en charge des outils pour les versions de MongoDB

Dans la mesure où vous effectuez une migration à partir d'une version particulière de MongoDB, les outils pris en charge sont présentés ci-dessous :

![Versions de MongoDB prises en charge par les outils de migration.](./media/mongodb-pre-migration/migration-tool-compatibility.png)

### <a name="post-migration"></a>Postmigration

Lors de la phase de prémigration, prenez le temps de planifier les étapes à suivre pour la migration et l'optimisation des applications après la migration.
* Lors de la phase de postmigration, vous procéderez au basculement de votre application pour utiliser Azure Cosmos DB à la place de votre patrimoine de données MongoDB existant. 
* Faites de votre mieux pour planifier l'indexation, la distribution globale, la cohérence et d'autres propriétés Azure Cosmos DB *mutables* au niveau de chaque ressource. Ces paramètres de configuration d'Azure Cosmos DB *peuvent* toutefois être modifiés ultérieurement. Attendez-vous donc à devoir procéder à des ajustements de ces paramètres en cours de route. Ne laissez pas ces aspects devenir une cause de paralysie de l'analyse. Vous appliquerez ces configurations mutables après la migration.
* Vous trouverez [ici](mongodb-post-migration.md) le meilleur guide postmigration.

## <a name="next-steps"></a>Étapes suivantes
* [Migrer vos données MongoDB vers Cosmos DB à l’aide de Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisionner le débit sur les conteneurs et les bases de données Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
* [Distribution de données mondiale avec Azure Cosmos DB](distribute-data-globally.md)
* [Indexation dans Azure Cosmos DB](index-overview.md)
* [Unités de requête dans Azure Cosmos DB](request-units.md)
