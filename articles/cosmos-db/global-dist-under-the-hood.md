---
title: Distribution mondiale avec Azure Cosmos DB - Sous le capot
description: Cet article fournit des détails techniques sur la distribution mondiale d’Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: a599be52575ed06cdb3a3713fc2f0915ab2f6c2b
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407485"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribution de données mondiale avec Azure Cosmos DB - Sous le capot

Azure Cosmos DB étant un service de base dans Azure, il est déployé dans toutes les régions Azure dans le monde entier, y compris le public souverains, ministère de la défense (DoD) et les clouds government. À l’intérieur d’un centre de données, nous déployons et gérons Azure Cosmos DB sur des tampons massifs de machines, chacune avec un stockage local dédié. À l’intérieur d’un centre de données, Cosmos DB est déployé dans de nombreux clusters, chacun pouvant exécuter plusieurs générations de matériel. Ordinateurs d’un cluster sont généralement réparties sur 10 à 20 domaines d’erreur pour la haute disponibilité au sein d’une région. L’image suivante montre la topologie du système de distribution mondiale Cosmos DB :

![Topologie du système](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Distribution globale dans Azure Cosmos DB est la clé en main :** À tout moment, en quelques clics, ou par programmation avec un seul appel d’API, vous pouvez ajouter ou supprimer des régions géographiques associées à votre base de données Cosmos. Une base de données Cosmos, à son tour, consistant en un ensemble de conteneurs de Cosmos. Dans Cosmos DB, les conteneurs font office d’unités logiques de distribution et d’extensibilité. Les collections, tables et graphiques que vous créez sont (en interne) tout simplement des conteneurs Cosmos. Les conteneurs sont complètement indépendante du schéma et fournissent une étendue pour une requête. Les données d’un conteneur Cosmos sont automatiquement indexées lors de l’ingestion. Indexation automatique permet aux utilisateurs d’interroger les données sans vous soucier de la gestion des schémas ou index, en particulier dans une configuration mondialement distribuée.  

- Dans une région donnée, les données d’un conteneur sont distribuées à l’aide d’une clé de partition, que vous fournissez et êtes géré en toute transparence par les partitions physiques sous-jacentes (*distribution locale*).  

- Chaque partition physique est également répliquée entre régions géographiques (*distribution mondiale*). 

Quand une application à l’aide de Cosmos DB en toute flexibilité met à l’échelle de débit sur un conteneur Cosmos ou consomme plus de stockage, Cosmos DB gère en toute transparence les opérations de gestion de partition (fractionner, cloner, supprimer) dans toutes les régions. Indépendamment de l’échelle, de la distribution ou des défaillances, Cosmos DB continue à fournir une illustration système unique des données à l’intérieur des conteneurs globalement distribués dans un nombre quelconque de régions.  

Comme indiqué dans l’image suivante, les données dans un conteneur sont distribuées selon deux critères : au sein d’une région et entre des régions, dans le monde entier :  

![partition physique](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Une partition physique est implémentée par un groupe de réplicas, appelé un *jeu de réplicas*. Chaque ordinateur héberge des centaines de réplicas qui correspondent aux différentes partitions physiques au sein d’un ensemble fixe de processus, comme illustré dans l’image ci-dessus. Les réplicas correspondant aux partitions physiques sont distribués de façon dynamique, et leur charge est équilibrée sur les machines au sein d’un cluster, et sur les centres de données au sein d’une région.  

Un réplica n’appartient qu’à un seul client Azure Cosmos DB. Chaque réplica héberge une instance du [moteur de base de données](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) de Cosmos DB, qui gère les ressources ainsi que les index associés. Le moteur de base de données de Cosmos DB opère sur un système type basé sur ARS. Le moteur est agnostique au concept d’un schéma, atténuant la frontière entre les valeurs de structure et d’instance d’enregistrements. Cosmos DB parvient à une indépendance complète du schéma en indexant automatiquement et efficacement tout ce qui est ingéré, ce qui permet aux utilisateurs d’interroger leurs données globalement distribuées sans devoir se préoccuper du schéma ou de la gestion d’index.

Le moteur de base de données Cosmos est constitué de composants, y compris la mise en œuvre de plusieurs primitives de coordination, runtimes de langage, le processeur de requêtes et le stockage et l’indexation des sous-systèmes de stockage transactionnel et l’indexation des données, respectivement. Pour assurer la durabilité et la haute disponibilité, le moteur de base de données conserve ses données et son index sur des disques SSD, et les réplique sur les instances de moteur de base de données à l’intérieur des jeux de réplicas. Clients plus volumineux correspondent à plus grande échelle du débit et du stockage et soit plus grande ou davantage de réplicas ou les deux. Chaque composant du système étant entièrement asynchrone, jamais un thread ne bloque, et chaque thread effectue un travail de courte durée n’induisant pas de basculements de thread superflus. Une limitation du débit et une contre-pression sont appliquées à la pile entière, du contrôle d’admission à tous les chemins d’E/S. Moteur de base de données COSMOS est conçu pour exploiter affinent la concurrence et fournissent un débit élevé en exploitant au sein de peu gourmant quantités de ressources système.

Distribution mondiale de COSMOS DB s’appuie sur deux abstractions clés – *jeux de réplicas* et *groupes de partitions*. Un jeu de réplicas est un bloc modulaire utilisé pour la coordination, et un groupe de partitions est une superposition dynamique de partitions physiques distribuées géographiquement. Pour comprendre le fonctionnement de la distribution globale, nous devons comprendre ces deux abstractions clés. 

## <a name="replica-sets"></a>Jeux de réplicas

Une partition physique est matérialisée sous la forme d’un groupe de réplicas auto-géré et dont la charge est équilibrée de façon dynamique, réparti sur plusieurs domaines d’erreur, appelé jeu de réplicas. Celui-ci implémente collectivement le protocole de machine à état répliqué pour rendre les données de la partition physique hautement disponibles, durables et cohérentes. L’appartenance au jeu de réplicas *N* est dynamique : il maintient fluctue entre *NMin* et *NMax* basé sur les échecs, les opérations d’administration et l’heure pour le basculement réplicas de régénérer/restauration. En fonction des changements d’appartenance, le protocole de réplication reconfigure également la taille des quorums de lecture et d’écriture. Pour répartir uniformément le débit est affecté à une partition physique donnée, nous utilisons deux idées : 

- Tout d’abord, le coût de traitement des demandes d’écriture sur le responsable est plus élevé que celui de l’application les mises à jour sur l’esclave. En conséquence, davantage de ressources système son budgétées pour le leader en comparaison des abonnés. 

- Deuxièmement, autant que possible, le quorum de lecture pour un niveau de cohérence donné est composé exclusivement des réplicas d’abonnés. Nous évitons de contacter le leader pour servir les lectures, sauf si cela est indispensable. Nous utilisons un nombre d’idées à partir de recherches effectuées sur la relation de [charge et votre capacité](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) dans les systèmes basés sur le quorum pour le [cinq modèles de cohérence](consistency-levels.md) qui Cosmos DB prend en charge.  

## <a name="partition-sets"></a>Groupes de partitions

Un groupe de partitions physiques, une de chacune des régions configurées avec la base de données Cosmos, est composé pour gérer le même ensemble de clés répliqué dans toutes les régions configurées. Cette primitive de coordination supérieure est appelée groupe de partitions. Il s’agit d’une superposition dynamique distribuée géographiquement de partitions physiques gérant un ensemble de clés donné. Si une partition physique (un jeu de réplicas) est circonscrite à l’intérieur d’un cluster, un jeu de partitions peut couvrir plusieurs clusters, centres de données et régions géographiques, comme le montre l’illustration suivante :  

![Jeux de partitions](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Vous pouvez considérer un jeu de partitions comme un « super jeu de réplicas » dispersé géographiquement, composé de plusieurs jeux de réplicas possédant le même ensemble de clés. Comme un jeu de réplicas, l’adhésion d’un jeu de partitions est également dynamique : elle fluctue en fonction d’opérations implicites de gestion des partitions physique pour ajouter/supprimer de nouvelles partitions à/d’un jeu de partitions donné (par exemple, lorsque vous étendez le débit sur un conteneur, ajoutez/supprimez une région à votre base de données Cosmos, ou lorsque des erreurs surviennent). Comme chacune des partitions (d’un ensemble de partitions) gère l’adhésion à l’ensemble de partitions dans sa propre réplique, l’adhésion est entièrement décentralisée et hautement disponible. Lors de la reconfiguration d’un groupe de partitions, la topologie de la superposition entre les partitions physiques est également établie. La topologie est sélectionnée de façon dynamique en fonction du niveau de cohérence, de la distance géographique et de la bande passante réseau disponible entre les partitions physiques source et cible.  

Le service vous permet de configurer vos bases de données Cosmos avec une ou plusieurs régions d’écriture. Selon le choix opéré, des groupes de partitions sont configurés pour accepter les écritures dans une région précise ou dans toutes les régions. Le système utilise un protocole consensuel imbriqué à deux niveaux. Un niveau opère à l’intérieur des réplicas d’un jeu de réplicas d’une partition physique acceptant les écritures, et l’autre opère au niveau d’un groupe de partitions pour fournir des garanties d’ordre complètes pour toutes les écritures validées à l’intérieur du groupe de partitions. Ce consensus imbriqué à plusieurs niveaux est vital pour l’implémentation de nos contrats SLA rigoureux en matière de haute disponibilité, ainsi que pour l’implémentation des modèles de cohérence que Cosmos DB offre à ses clients.  

## <a name="conflict-resolution"></a>Résolution des conflits

Notre conception de la propagation des mises à jour, de la résolution des conflits et du suivi de la causalité s’inspire de travaux antérieurs menés sur les [algorithmes épidémiques](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) et le système [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf). Si les noyaux des idées ont survécu et offrent un cadre de référence commode pour communiquer la conception de système de Cosmos DB, ils ont également subi une transformation importante au fur et à mesure de leur application au système Cosmos DB. Cela était nécessaire car les systèmes précédents n’étaient conçus ni avec la gouvernance des ressources, ni avec l’échelle à laquelle Cosmos DB doit opérer, ni pour offrir les capacités (par exemple, cohérence en fonction de l’obsolescence limitée) et les contrats SLA rigoureux et complets que Cosmos DB offre à ses clients.  

Rappelons qu’un groupe de partitions est distribué dans plusieurs régions et suit le protocole de réplication (multimaître) de Cosmos DB pour répliquer les données sur les partitions physiques constituant un groupe de partitions donné. Chaque partition physique (d’un groupe de partitions) accepte des écritures et sert des lectures, généralement aux clients locaux dans cette région. Les écritures acceptées par une partition physique à l’intérieur d’une région sont validées durablement et rendues hautement disponibles à l’intérieur de la partition physique avant d’être confirmées au client. Il s’agit d’écritures provisoires propagées à d’autres partitions physiques à l’intérieur du groupe de partitions à l’aide d’un canal anti-entropie. Les clients peuvent demander des écritures provisoires ou validées en transmettant un en-tête de requête. La propagation anti-entropie (y compris la fréquence de propagation) est dynamique, basée sur la topologie du groupe de partitions, la proximité régionale des partitions physiques et le niveau de cohérence configuré. À l’intérieur d’un groupe de partitions, Cosmos DB suit un schéma de validation principal avec une partition arbitre sélectionnée de manière dynamique. La sélection de l’arbitre est dynamique et fait partie intégrante de la reconfiguration du jeu de partitions en fonction de la topologie de la superposition. Les écritures validées (dont les mises à plusieurs lignes/par lots) sont garanties ordonnées. 

Nous employons des horloges vectorielles codées (contenant un identificateur de région et des horloges logiques correspondant à chaque niveau de consensus respectivement du jeu de réplicas et du groupe de partitions) pour le suivi de la causalité et les vecteurs de version afin de détecter et résoudre les conflits de mises à jour. La topologie et l’algorithme de sélection de pair sont conçus pour assurer un stockage fixe minimal et une surcharge réseau minimale des vecteurs de version. L’algorithme garantit la propriété de convergence stricte.  

Pour les bases de données Cosmos configurées avec plusieurs régions d’écriture, le système offre des stratégies flexibles de résolution automatique des conflits parmi lesquelles les développeurs peuvent choisir, notamment : 

- Stratégie Dernière écriture prioritaire (LWW) qui, par défaut, utilise une propriété timestamp définie par le système (basée sur le protocole d’horloge de synchronisation de l’heure). Cosmos DB vous permet également de spécifier toute autre propriété numérique personnalisée à utiliser pour la résolution des conflits.  
- Stratégie personnalisée de résolution des conflits (exprimée via des procédures de fusion) définie par l’application, conçue pour la réconciliation de sémantique définie par l’application des conflits. Ces procédures sont appelées lors de la détection de conflits d’écriture-écriture sous les auspices d’une transaction de base de données côté serveur. Le système fournit exactement une garantie pour l’exécution d’une procédure de fusion dans le cadre du protocole d’engagement. Plusieurs échantillons sont à votre disposition.  

## <a name="consistency-models"></a>Modèles de cohérence

Que vous configuriez votre base de données Cosmos avec une ou plusieurs régions d’écriture, vous pouvez choisir parmi cinq modèles de cohérence bien définis. En lien avec la prise en charge nouvellement ajoutée de l’activation de plusieurs régions d’écriture, voici quelques aspects remarquables des niveaux de cohérence :  

Comme précédemment, la cohérence en fonction de l’obsolescence limitée garantit que toutes les lectures auront lieu dans les k préfixes ou t secondes suivant la dernière écriture dans toute région. De plus, les lectures avec cohérence en fonction de l’obsolescence limitée sont garanties unitones et assorties de garanties de préfixe cohérent. Le protocole anti-entropie opère avec une limite de débit, et garantit que les préfixes ne s’accumulent pas et que la contre-pression sur les écritures ne doive pas être appliquée. Comme précédemment, la cohérence de session garantit une lecture et une écriture unitones, la lecture de votre propre écriture, que l’écriture suive la lecture, et offre des garanties à préfixe cohérent dans le monde entier. Pour les bases de données configurées avec une cohérence forte, les avantages du multimaître (faible latence d’écriture, haute disponibilité d’écriture) ne s’appliquent pas, en raison de la réplication synchrone entre les régions.

Les sémantiques des cinq modèles de cohérence dans Cosmos DB sont exposées [ici](consistency-levels.md) et décrites mathématiquement [ici](https://github.com/Azure/azure-cosmos-tla) à l’aide d’une spécification TLA+ de haut niveau.

## <a name="next-steps"></a>Étapes suivantes

Découvrez ensuite comment configurer la distribution mondiale à l’aide les articles suivants :

* [Article relatif à la configuration des clients pour le multihébergement](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Ajouter/supprimer des régions à partir de votre compte de base de données](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Article relatif à la création d’une stratégie de résolution de conflits personnalisée pour les comptes SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
