---
title: Forum aux questions (FAQ) - Azure Event Hubs pour Apache Kafka
description: Cet article explique comment les contrôles serveur consommateurs et les producteurs qui utilisent des protocoles différents (AMQP, Apache Kafka et HTTPS) peuvent échanger des événements avec Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606684"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Forum aux questions (FAQ) - Event Hubs pour Apache Kafka 
Cet article fournit des réponses à certaines des questions fréquemment posées concernant la migration vers Event Hubs pour Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Exécutez-vous Apache Kafka ?

Non.  Nous exécutons les opérations de l’API Kafka dans l’infrastructure Event Hubs.  Étant donné qu’il existe une corrélation étroite entre la fonctionnalité AMQP dans Apache Kafka et Event Hubs (c’est-à-dire la production, la réception, la gestion, etc.), nous sommes en mesure de transférer la fiabilité connue d’Event Hubs à l’espace PaaS Kafka.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Groupe de consommateurs Event Hubs et groupe de consommateurs Kafka
Quelle est la différence entre un groupe de consommateurs Event Hub et un groupe de consommateurs Kafka sur Event Hubs ? Les groupes de consommateurs Kafka sur Event Hubs sont entièrement distincts des groupes de consommateurs Event Hubs standard.

**Groupes de consommateurs Event Hubs**

- Ils sont gérés avec des opérations de création, de récupération, de mise à jour et de suppression (CRUD) via le portail, le Kit de développement logiciel (SDK) ou des modèles Azure Resource Manager. Les groupes de consommateurs Event Hubs ne peuvent pas être autocréés.
- Il s’agit d’entités enfants d’un hub d’événements. Cela signifie que le même nom de groupe de consommateurs peut être réutilisé entre les hubs d’événements d’un même espace de noms car ce sont des entités distinctes.
- Elles ne sont pas utilisées pour stocker des décalages. La consommation AMQP orchestrée s’effectue à l’aide d’un stockage de décalage externe, par exemple, Stockage Azure.

**Groupes de consommateurs Kafka**

- Ils sont autocréés.  Les groupes Kafka peuvent être gérés via les API du groupe de consommateurs Kafka.
- Ils peuvent stocker des décalages dans le service Event Hubs.
- Ils sont utilisés en tant que clés dans ce qui est effectivement un stockage clé-valeur de décalage. Pour une paire unique regroupant `group.id` et `topic-partition`, nous stockons un décalage dans Stockage Azure (triple réplication). Les utilisateurs Event Hubs n’entraînent pas de frais supplémentaires pour le stockage des décalages Kafka. Les décalages peuvent être gérés via les API du groupe de consommateurs Kafka, mais les *comptes* de stockage de décalage ne sont pas directement visibles ou gérables pour les utilisateurs du hub d’événements.  
- Ils s’étendent sur un espace de noms. L’utilisation du même nom de groupe Kafka pour plusieurs applications sur plusieurs rubriques signifie que la totalité des applications et leurs clients Kafka sera rééquilibrée chaque fois qu’une seule application nécessite un rééquilibrage.  Choisissez avec soin vos noms de groupes.
- Ils sont entièrement distincts des groupes de consommateurs Event Hubs. Vous **n’avez pas** besoin d’utiliser « $Default », pas plus que vous n’avez à vous soucier des clients Kafka interférant avec les charges de travail AMQP.
- Ils n’apparaissent pas dans le portail Azure. Les informations sur le groupe de consommateurs sont accessibles via les API Kafka.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Event Hubs et sur Event Hubs pour Kafka, consultez les articles suivants :  

- [Guide du développeur Apache Kafka pour Event Hubs](apache-kafka-developer-guide.md)
- [Guide de migration Apache Kafka pour Event Hubs](apache-kafka-migration-guide.md)
- [Guide de dépannage Apache Kafka pour Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Configurations recommandées](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

