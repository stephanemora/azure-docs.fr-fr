---
title: Choisissez une analytique en temps réel et diffusion en continu de la technologie de traitement sur Azure
description: Découvrez comment choisir l’analytique en temps réel vers la droite et la technologie de traitement de streaming pour générer votre application sur Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 85d6ed80da93f90e6dc0feaee7081ee3f36f1bf9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242690"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Choisissez une analytique en temps réel et diffusion en continu de la technologie de traitement sur Azure

Plusieurs services sont disponibles pour le traitement de diffusion en continu sur Azure et analytique en temps réel. Cet article fournit les informations nécessaires déterminer la technologie la mieux adaptée à votre application.

## <a name="when-to-use-azure-stream-analytics"></a>Quand utiliser Azure Stream Analytique

Azure Stream Analytique est le service recommandé pour l’analytique de flux de données sur Azure. Il est destiné à un large éventail de scénarios qui incluent, mais ne sont pas limités à :

* Tableaux de bord pour la visualisation des données
* En temps réel [alertes](stream-analytics-set-up-alerts.md) à partir de modèles temporelles et spatiales ou des anomalies
* Extraire, transformer, charger (ETL)
* [Modèle d’approvisionnement en événements](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Ajout d’une Analytique de Stream Azure le travail à votre application est le moyen le plus rapide pour obtenir de diffusion en continu analytique d’et en cours d’exécution dans Azure, en utilisant le langage SQL que vous connaissez déjà. Azure Stream Analytique est un service de travaux, afin que vous n’êtes pas obligé de passer des clusters de gestion de temps, et il est inutile à vous soucier des temps d’arrêt avec un contrat SLA de 99,9 % au niveau du travail. Également, la facturation est effectuée au niveau du travail rendre les coûts de démarrage faible (une seule unité de Streaming), mais évolutif (jusqu'à 192 unités de diffusion en continu). Il est beaucoup plus économique pour exécuter des tâches de Stream Analytique quelques plutôt que d’exécuter et de maintenir un cluster.

Azure Stream Analytique a une riche expérience d’out-of-the-box. Vous pouvez immédiatement tirer parti des fonctionnalités suivantes sans aucune installation supplémentaire :

* Les opérateurs temporels intégrés, tels que [fenêtrées](stream-analytics-window-functions.md), jointures temporelles et fonctions analytiques temporelles.
* Azure natif [d’entrée](stream-analytics-add-inputs.md) et [sortie](stream-analytics-define-outputs.md) adaptateurs
* Prise en charge pour la modification lente [données de référence](stream-analytics-use-reference-data.md) (également appelé une recherche des tables), y compris la liaison avec des données géospatiales référence gardiennage virtuel.
* Des solutions, telles que [détection d’anomalie](stream-analytics-machine-learning-anomaly-detection.md)
* Plusieurs fenêtres de temps dans la même requête
* Capacité à composer plusieurs opérateurs temporels dans les séquences arbitraires.
* Sous 100 ms, latence de bout en bout à partir de l’entrée arrivant à Event Hubs, à la sortie d’accueil dans Event Hubs, y compris le délai réseau depuis et vers Event Hubs, à haut débit soutenu

## <a name="when-to-use-other-technologies"></a>Quand utiliser d’autres technologies

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Vous avez besoin d’entrée à partir d’ou vers Kafka

Azure Stream Analytique ne comporte une entrée d’Apache Kafka ou adaptateur de sortie. Si vous avez les événements de lancement ou que vous devez envoyer à Kafka et que vous n’avez pas obligatoire pour l’exécution de votre propre cluster Kafka, vous pouvez continuer à utiliser l’Analytique de Stream en envoyant des événements aux Hubs d’événements à l’aide de l’API Event Hubs Kafka sans modifier l’expéditeur d’événement. Si vous n’avez pas besoin exécuter votre propre cluster Kafka, vous pouvez utiliser Spark Structured Streaming, qui est entièrement pris en charge [Azure Databricks](../azure-databricks/index.yml), ou Storm sur [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Vous souhaitez écrire des UDF, les agrégats et désérialiseur personnalisé dans une langue autre que JavaScript ouC#

Azure Stream Analytique prend en charge les fonctions définies par l’utilisateur (UDF) ou agrégats définis par l’utilisateur (UDA) dans JavaScript pour les travaux de cloud et C# pour les travaux de IoT Edge. C#désérialiseurs défini par l’utilisateur sont également prises en charge. Si vous souhaitez implémenter un désérialiseur, une fonction UDF ou une instance UDA dans d’autres langages, tels que Java ou Python, vous pouvez utiliser Spark Structured Streaming. Vous pouvez également exécuter les Hubs d’événements **EventProcessorHost** sur vos propres machines virtuelles pour le traitement de diffusion en continu arbitraire.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Votre solution est dans un environnement incluant plusieurs cloud ou en local

Azure Stream Analytique est une technologie propriétaire de Microsoft et est uniquement disponible sur Azure. Si vous avez besoin de votre solution soit portable sur les Clouds ou localement, envisagez les technologies open source tels que Structured Streaming Spark ou Storm.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un travail Stream Analytique à l’aide du portail Azure](stream-analytics-quick-create-portal.md)
* [Créer un travail Stream Analytique à l’aide d’Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Créer un travail Stream Analytique à l’aide de Visual Studio](stream-analytics-quick-create-vs.md)
* [Créer un travail Stream Analytique à l’aide de Visual Studio Code](quick-create-vs-code.md)