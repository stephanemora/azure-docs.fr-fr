---
title: Optimisation des performances pour les clusters Apache Kafka HDInsight
description: Fournit une vue d’ensemble des techniques pour optimiser les charges de travail Apache Kafka sur Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 903cd8921801ffb47dd73f48e507f30aa0b6dccc
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373149"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimisation des performances pour les clusters Apache Kafka HDInsight

Cet article donne des suggestions pour optimiser les performances de vos charges de travail Apache Kafka dans HDInsight. Le focus est sur le réglage de producteur et la configuration du service broker. Il existe différentes façons de mesurer les performances et les optimisations que vous appliquez dépend de vos besoins.

## <a name="architecture-overview"></a>Présentation de l'architecture

Les rubriques Kafka sont utilisés pour organiser des enregistrements. Enregistrements sont produits par les producteurs et consommées par les consommateurs. Producteurs envoie des enregistrements au répartiteurs Kafka, qui ensuite stockent les données. Chaque nœud Worker dans votre cluster HDInsight est un répartiteur Kafka.

Les rubriques partitionnent les enregistrements entre les répartiteurs. Au cours de l’utilisation des enregistrements, il est possible d’utiliser jusqu’à un consommateur par partition pour effectuer un traitement parallèle des données.

La réplication permet de dupliquer des partitions entre les nœuds. Cela protège contre les pannes de nœud (broker). Une seule partition entre le groupe de réplicas est désignée comme le leader de la partition. Le trafic de producteur est acheminé vers le leader de chaque nœud, en utilisant l’état géré par ZooKeeper.

## <a name="identify-your-scenario"></a>Identifier votre scénario

Performances d’Apache Kafka a deux aspects principaux : débit et la latence. Le débit est le taux maximal auquel les données peuvent être traitées. Un débit plus élevé est généralement préférable. La latence est le temps que nécessaire pour les données à stocker ou à récupérer. Une latence plus faible est généralement préférable. Trouver le juste équilibre entre le débit, la latence et le coût de l’infrastructure de l’application peut être difficile. Vos exigences de performances probablement correspond à l’une des trois courantes situations suivantes, selon si vous avez besoin d’un débit élevé, faible latence ou les deux :

* Débit élevé, faible latence. Ce scénario nécessite un débit élevé et faible latence (environ 100 millisecondes). Un exemple de ce type d’application est service analyse des disponibilités.
* Un débit élevé, latence élevée. Ce scénario nécessite un débit élevé (~1.5 Gbits/s), mais peut tolérer une latence plus élevée (< 250 ms). Un exemple de ce type d’application est l’ingestion des données de télémétrie pour près de processus en temps réel, tels que les applications de détection des intrusions et de sécurité.
* Faible débit, la faible latence. Ce scénario nécessite une faible latence (< 10 ms) pour le traitement en temps réel, mais peut tolérer un débit inférieur. Un exemple de ce type d’application est les vérifications de grammaire et en ligne.

## <a name="producer-configurations"></a>Configurations de producteur

Les sections suivantes met en évidence certaines des propriétés de configuration plus importantes pour optimiser les performances de votre producteurs de Kafka. Pour obtenir une explication détaillée de toutes les propriétés de configuration, consultez [documentation Apache Kafka sur les configurations de producteur](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Taille du lot

Producteurs d’Apache Kafka assembler des groupes de messages (appelés lots) qui sont envoyés en tant qu’unité à stocker dans une partition de stockage unique. Taille de lot : le nombre d’octets qui doivent être présents avant la transmission de ce groupe. Augmentant la `batch.size` paramètre peut augmenter le débit, car il réduit le traitement du traitement des demandes d’e/s et de réseau. Sous une charge faible, taille de lot accrue peut augmenter la latence d’envoi Kafka comme le producteur attend qu’un lot soit prête. Sous une charge importante, il est recommandé d’augmenter la taille de lot pour améliorer le débit et la latence.

### <a name="producer-required-acknowledgements"></a>Accusés de réception de producteur requis

Le producteur requis `acks` configuration détermine le nombre d’accusés de réception requis par le responsable de la partition avant qu’une demande d’écriture est considéré comme terminé. Ce paramètre affecte la fiabilité des données et il prend les valeurs de `0`, `1`, ou `-1`. La valeur de `-1` signifie qu’un accusé de réception doit être reçue à partir de tous les réplicas avant la fin de l’écriture. Paramètre `acks = -1` fournit les meilleures garanties contre la perte de données, mais également des résultats dans une latence plus élevée et la réduction du débit. Si votre application exigent un débit plus élevé, essayez de définir `acks = 0` ou `acks = 1`. N’oubliez pas, ce qui peut réduire la fiabilité des données ne reconnaissent ne pas tous les réplicas.

### <a name="compression"></a>Compression

Un producteur Kafka peut être configuré pour compresser les messages avant de les envoyer aux courtiers. Le `compression.type` paramètre spécifie le codec de compression à utiliser. Les codecs de compression pris en charge sont « gzip », « rapide » et « lz4. » La compression est utile et doit être considéré comme s’il existe une restriction sur la capacité du disque.

Parmi les codecs de compression couramment utilisés deux, `gzip` et `snappy`, `gzip` a un taux de compression plus élevé, ce qui entraîne l’utilisation du disque inférieur au prix d’une charge plus élevée du processeur. Le `snappy` codec fournit moins de compression avec moins de surcharge du processeur. Vous pouvez décider du codec à utiliser en fonction des limitations de processeur de disque ou le producteur broker. `gzip` Compresser les données à un rythme cinq fois supérieure à `snappy`.

À l’aide de la compression de données augmentera le nombre d’enregistrements qui peuvent être stockées sur un disque. Elle peut également augmenter une surcharge du processeur dans les cas où il existe une incompatibilité entre les formats de compression utilisés par le producteur et le service broker. les données doivent être compressées avant d’envoyer et décompresser avant leur traitement.

## <a name="broker-settings"></a>Paramètres du service Broker

Les sections suivantes met en évidence certains des paramètres plus importantes pour optimiser les performances de votre répartiteurs Kafka. Pour obtenir une explication détaillée de tous les paramètres du service broker, consultez [documentation Apache Kafka sur les configurations de producteur](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Nombre de disques

Disques de stockage ont limité les e/s (entrée/sortie opérations par seconde) et en lecture/écriture d’octets par seconde. Lorsque vous créez de nouvelles partitions, Kafka stocke chaque nouvelle partition sur le disque avec moins de partitions existantes pour les équilibrer les disques disponibles. En dépit de la stratégie de stockage, lors du traitement des centaines de réplicas de partition sur chaque disque, de Kafka peut saturer facilement le débit de disque disponible. L’inconvénient ici est entre le débit et de coût. Si votre application nécessite un débit supérieur, créer un cluster avec plusieurs gérés disques par service broker. HDInsight ne prend pas en charge l’ajout de disques gérés à un cluster en cours d’exécution. Pour plus d’informations sur la façon de configurer le nombre de disques gérés, consultez [configurer le stockage et l’extensibilité pour Apache Kafka sur HDInsight](apache-kafka-scalability.md). Comprendre les implications en matière de coût croissant d’espace de stockage pour les nœuds de votre cluster.

### <a name="number-of-topics-and-partitions"></a>Nombre de rubriques et partitions

Écrivent des producteurs de Kafka vers des rubriques. Les consommateurs Kafka lire à partir de rubriques. Une rubrique est associée à un journal, qui est une structure de données sur le disque. Kafka ajoute les enregistrements à partir d’un ou à la fin d’un journal de la rubrique. Un journal de la rubrique se compose de nombreuses partitions réparties sur plusieurs fichiers. Ces fichiers sont, à son tour, réparties sur plusieurs nœuds de cluster Kafka. Consommateurs lisent à partir des rubriques Kafka à leur rythme et et vous pouvez choisir de leur position (offset) dans le journal de la rubrique.

Chaque partition Kafka est un fichier journal sur le système, et les threads producteur peuvent écrire dans plusieurs journaux simultanément. De même, étant donné que chaque thread de consommateur lit les messages à partir d’une seule partition, consommation à partir de plusieurs partitions est gérée en parallèle ainsi.

En augmentant la densité de la partition (le nombre de partitions par service broker) ajoute une surcharge liée aux opérations de métadonnées et par partition demande/réponse entre le responsable de la partition et ses abonnés. Même en l’absence de données transitant par, les réplicas de partition extrait toujours les données de leaders, ce qui entraîne un traitement supplémentaire pour envoyer et recevoir des demandes via le réseau.

Pour Apache Kafka clusters 1.1 et ci-dessus dans HDInsight, nous vous recommandons d’avoir un maximum de 1 000 partitions par service broker, y compris les réplicas. Augmentation du nombre de partitions par service broker diminue le débit et peut également entraîner l’indisponibilité de la rubrique. Pour plus d’informations sur la prise en charge de la partition Kafka, consultez [le billet de blog officiel Apache Kafka sur l’augmentation du nombre de partitions pris en charge dans la version 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Pour plus d’informations sur la modification des rubriques, consultez [Apache Kafka : modification des rubriques](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Nombre de réplicas

Facteur de réplication plus élevée entraîne des demandes supplémentaires entre le responsable de la partition et les abonnés. Par conséquent, un facteur de réplication supérieure consomme plus de disque et de processeur pour gérer les demandes supplémentaires, augmentant écrire la latence et la diminution de débit.

Nous vous recommandons d’utiliser au moins 3 réplications pour Kafka dans Azure HDInsight. Régions plus Azure disposent de trois domaines d’erreur, mais dans des régions avec uniquement deux domaines d’erreur, les utilisateurs doivent utiliser la réplication x 4.

Pour plus d’informations sur la réplication, consultez [Apache Kafka : réplication](https://kafka.apache.org/documentation/#replication) et [Apache Kafka : augmentation du facteur de réplication](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Étapes suivantes

* [Traitement de billions d’événements par jour avec Apache Kafka sur Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Présentation d’Apache Kafka sur HDInsight](apache-kafka-introduction.md)
