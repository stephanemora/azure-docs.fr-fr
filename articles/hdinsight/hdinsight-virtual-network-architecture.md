---
title: Architecture de réseau virtuel Azure HDInsight
description: Découvrez les ressources disponibles lorsque vous créez un cluster HDInsight dans un réseau virtuel Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 6d92273298c0448d7377acab6f3b8ea1cc1ed908
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762920"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architecture de réseau virtuel Azure HDInsight

Cet article explique les ressources qui sont présentes lorsque vous déployez un cluster HDInsight dans un réseau virtuel Azure personnalisé. Ces informations vous aidera à connecter des ressources locales sur votre cluster HDInsight dans Azure. Pour plus d’informations sur les réseaux virtuels Azure, consultez [What ' s réseau virtuel Azure ?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Types de ressources dans des clusters Azure HDInsight

Les clusters Azure HDInsight ont différents types de machines virtuelles ou des nœuds. Chaque type de nœud joue un rôle dans le fonctionnement du système. Le tableau suivant résume ces types de nœuds et leurs rôles dans le cluster.

| Type | Description |
| --- | --- |
| Nœud principal |  Pour tous les types de cluster à l’exception d’Apache Storm, les nœuds principaux hébergent les processus qui gèrent l’exécution de l’application distribuée. Le nœud principal est également le nœud que vous pouvez exécuter SSH dans et exécuter des applications qui sont ensuite coordonnées pour s’exécuter sur les ressources de cluster. Le nombre de nœuds principaux est fixé à deux pour tous les types de cluster. |
| Nœud zooKeeper | Zookeeper coordonne les tâches entre les nœuds qui font le traitement des données. Également élection du responsable du nœud principal et effectue le suivi de quel nœud principal est en cours d’exécution un service maître spécifique. Le nombre de nœuds ZooKeeper est fixé à deux. |
| Nœud Worker | Représente les nœuds qui prennent en charge des fonctionnalités de traitement des données. Nœuds de travail peuvent être ajoutés ou supprimés du cluster à l’échelle des capacités de calcul et de gérer les coûts. |
| Nœud de périmètre R Server | Le nœud de périmètre R Server représente le nœud que vous pouvez exécuter SSH dans et exécuter des applications qui sont ensuite coordonnées pour s’exécuter sur les ressources de cluster. Un nœud de périmètre ne participe pas analyse des données au sein du cluster. Ce nœud héberge également R Studio Server, ce qui vous permet d’exécuter l’application de R à l’aide d’un navigateur. |
| Nœud de la région | Pour le type de cluster HBase, le nœud de la région (également appelé un nœud de données) s’exécute le serveur de région. Serveurs de région servent et gérer une partie des données gérées par HBase. Nœuds de la région peuvent être ajoutés ou supprimés du cluster à l’échelle des capacités de calcul et de gérer les coûts.|
| Nœud Nimbus | Pour le type de cluster Storm, le nœud Nimbus offre des fonctionnalités similaires pour le nœud principal. Le nœud Nimbus affecte des tâches à d’autres nœuds dans un cluster via Zookeeper, qui coordonne l’exécution de topologies Storm. |
| Nœud de superviseur | Pour le type de cluster Storm, le nœud de superviseur exécute les instructions fournies par le nœud Nimbus pour effectuer le traitement de votre choix. |

## <a name="basic-virtual-network-resources"></a>Ressources de réseau virtuel de base

Le diagramme suivant illustre le positionnement des nœuds de HDInsight et les ressources réseau dans Azure.

![Diagramme des entités HDInsight créés dans un réseau virtuel Azure personnalisé](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Les ressources par défaut présents lorsque HDInsight est déployé dans un réseau virtuel Azure incluent les types de nœud de cluster mentionnés dans le tableau précédent, ainsi que les périphériques réseau qui prennent en charge la communication entre le réseau virtuel et en dehors des réseaux.

Le tableau suivant récapitule les nœuds de neuf cluster qui sont créés lorsque HDInsight est déployé dans un réseau virtuel Azure personnalisé.

| Type de ressource | Nombre présent | Détails |
| --- | --- | --- |
|Nœud principal | two |    |
|Nœud Zookeeper | three | |
|Nœud Worker | two | Ce nombre peut varier en fonction de la configuration du cluster et la mise à l’échelle. Un minimum de trois nœuds de travail est nécessaire pour Apache Kafka.  |
|Nœud de passerelle | two | Nœuds de passerelle sont des machines virtuelles qui sont créées dans Azure, mais ne sont pas visibles dans votre abonnement. Contactez le support technique si vous avez besoin de redémarrer ces nœuds. |

Ressources réseau suivantes présents sont automatiquement créés à l’intérieur du réseau virtuel utilisé avec HDInsight :

| Ressources de mise en réseau | Nombre présent | Détails |
| --- | --- | --- |
|Équilibrage de charge | three | |
|Interfaces réseau | neuf | Cette valeur est basée sur un cluster normal, où chaque nœud possède sa propre interface réseau. Les neuf interfaces sont pour les deux nœuds principaux, trois nœuds zookeeper, deux nœuds de travail et deux nœuds de passerelle mentionnés dans le tableau précédent. |
|Adresses IP publiques | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Points de terminaison pour la connexion à HDInsight

Vous pouvez accéder à votre cluster HDInsight de trois façons :

- Un point de terminaison HTTPS en dehors du réseau virtuel à `CLUSTERNAME.azurehdinsight.net`.
- Un point de terminaison SSH pour se connecter directement au nœud principal à `CLUSTERNAME-ssh.azurehdinsight.net`.
- Un point de terminaison HTTPS dans le réseau virtuel `CLUSTERNAME-int.azurehdinsight.net`. Notez que le «-int » dans cette URL. Ce point de terminaison sera résolu en une adresse IP privée de ce réseau virtuel et n’est pas accessible à partir de l’internet public.

Ces trois points de terminaison sont affectés un équilibreur de charge.

Les adresses IP publiques sont également fournies aux deux points de terminaison permettant la connexion à partir de l’extérieur du réseau virtuel.

1. Une adresse IP publique est affectée à l’équilibrage de charge pour le nom de domaine complet (FQDN) à utiliser lors de la connexion au cluster à partir d’internet `CLUSTERNAME.azurehdinsight.net`.
1. La deuxième adresse IP publique est utilisée pour le nom de domaine uniquement SSH `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser le trafic entrant à des clusters HDInsight dans un réseau virtuel avec le point de terminaison privé](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
