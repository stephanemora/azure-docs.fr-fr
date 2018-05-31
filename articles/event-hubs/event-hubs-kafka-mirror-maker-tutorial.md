---
title: Utilisation de Kafka MirrorMaker avec Azure Event Hubs pour l’écosystème Kafka | Microsoft Docs
description: Utilisez Kafka MirrorMaker pour mettre en miroir un cluster Kafka dans Event Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 819071321d5609728e7c62abb5b25bf354107850
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941237"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Utilisation de Kafka MirrorMaker avec Event Hubs pour les écosystèmes Kafka

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs).

Une considération majeure pour des applications à l’échelle du cloud modernes est la possibilité de mettre à jour, d’améliorer et de modifier une infrastructure sans interrompre le service. Ce didacticiel montre comment un Event Hub compatible avec Kafka et Kafka MirrorMaker peuvent intégrer un pipeline Kafka existant dans Azure en « mettant en miroir » le flux d’entrée Kafka dans le service Event Hub. 

Un point de terminaison Kafka Azure Event Hubs vous permet de vous connecter à des Azure Event Hubs (c’est-à-dire, des clients Kafka) en utilisant le protocole Kafka. En apportant des modifications minimales à une application Kafka, vous pouvez vous connecter à Azure Event Hubs et profiter des avantages de l’écosystème Azure. Les Event Hubs compatibles avec Kafka prennent actuellement en charge les versions 1.0 et ultérieures de Kafka.

Cet exemple montre comment mettre en miroir un répartiteur Kafka dans un hub d’événements compatible avec Kafka à l’aide de Kafka MirrorMaker.

   ![Kafka MirrorMaker avec Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vérifiez que vous disposez des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargé](http://maven.apache.org/download.cgi) et [installé](http://maven.apache.org/install.html) une archive binaire Maven.
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/downloads)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Un espace de noms Event Hubs est requis pour échanger avec tout service Event Hubs. Pour obtenir des instructions sur l’obtention d’un point de terminaison Kafka pour Event Hubs, voir [Création d’un Event Hub compatible avec Kafka](event-hubs-create.md). Veillez à copier la chaîne de connexion Event Hubs pour une utilisation ultérieure.

## <a name="clone-the-example-project"></a>Cloner l’exemple de projet

À présent que vous avez une chaîne de connexion Event Hubs compatible avec Kafka, clonez le référentiel Azure Event Hubs et accédez au sous-dossier `mirror-maker` :

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurer un cluster Kafka

Utilisez le [guide de démarrage rapide de Kafka](https://kafka.apache.org/quickstart) pour configurer un cluster avec les paramètres souhaités (ou utilisez un cluster Kafka existant).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker permet la « mise en miroir » d’un flux. Étant donné des clusters Kafka source et cible, MirrorMaker garantit que tous les messages envoyés au cluster source sont reçus à la fois par les clusters source et cible. Cet exemple montre comment mettre en miroir un cluster Kafka source avec un Event Hub compatible avec Kafka. Ce scénario peut être utilisé pour envoyer des données d’un pipeline Kafka existant à Event Hubs sans interrompre le flux de données. 

Pour plus d’informations sur Kafka MirrorMaker, voir le [Guide Mise en miroir de Kafka/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Configuration

Pour configurer Kafka MirrorMaker, attribuez-lui un cluster Kafka en tant que consommateur/source, et un hub d’événements compatible avec Kafka en tant que producteur/destination.

#### <a name="consumer-configuration"></a>Configuration du consommateur

Mettez à jour le fichier de configuration du consommateur `source-kafka.config`, qui indique à MirrorMaker les propriétés du cluster Kafka source.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuration du producteur

Maintenant, mettez à jour le fichier de configuration du producteur `mirror-eventhub.config`, qui indique à MirrorMaker d’envoyer les données dupliquées (ou « en miroir ») au service Event Hubs. Modifiez en particulier `bootstrap.servers` et `sasl.jaas.config` pour pointer vers votre point de terminaison Kafka Event Hubs. Le service Event Hubs nécessite une communication sécurisée (SASL), qui est obtenue en définissant les trois dernières propriétés dans la configuration suivante : 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>Exécuter MirrorMaker

Exécutez le script Kafka MirrorMaker à partir du répertoire racine Kafka en utilisant les fichiers de configuration récemment mis à jour. Veillez à copier les fichiers de configuration dans le répertoire Kafka racine, ou à mettre à jour leurs chemins dans la commande suivante.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Pour vérifier que les événements atteignent le hub d’événements compatible avec Kafka, consultez les statistiques d’entrée sur le [portail Azure](https://azure.microsoft.com/features/azure-portal/), ou exécutez un consommateur sur le hub d’événements.

Lorsque MirrorMaker est en cours d’exécution, tous les événements envoyés au cluster Kafka source sont reçus à la fois par le cluster Kafka et par le service de hub d’événements compatible avec Kafka en miroir. En utilisant MirrorMaker et un point de terminaison Kafka Event Hubs, vous pouvez migrer un pipeline Kafka existant vers le service Azure Event Hubs géré sans modifier le cluster existant ou interrompre le flux de données en cours.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)
* [En savoir plus sur Event Hubs pour l’écosystème Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Apprenez-en davantage sur [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) pour diffuser en continu des événements à partir d’un Kafka local vers des hubs d’événements compatibles avec Kafka dans le cloud.
