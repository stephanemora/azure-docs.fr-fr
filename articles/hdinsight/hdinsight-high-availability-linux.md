---
title: Haute disponibilité pour Hadoop - Azure HDInsight
description: Découvrez comment les clusters HDInsight améliorent la fiabilité et la disponibilité en utilisant un nœud principal supplémentaire. Découvrez dans quelle mesure les services Hadoop tels qu’Ambari et Hive sont concernés, et comment se connecter à chaque nœud principal via SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: haute disponibilité hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381408"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Disponibilité et fiabilité des clusters Apache Hadoop dans HDInsight

Les clusters HDInsight fournissent deux nœuds principaux afin d’augmenter la disponibilité et la fiabilité des services et travaux Apache Hadoop en cours d’exécution.

Hadoop garantit de hauts niveaux de disponibilité et de fiabilité en répliquant des services et données sur plusieurs nœuds d’un cluster. Toutefois, les distributions standard de Hadoop ne comportent généralement qu’un seul nœud principal. Toute défaillance du nœud principal unique peut entraîner un arrêt de fonctionnement du cluster. HDInsight fournit deux nœuds principaux pour améliorer la disponibilité et la fiabilité de Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Disponibilité et fiabilité des nœuds

Les nœuds d’un cluster HDInsight sont implémentés à l’aide de machines virtuelles Azure. Les sections ci-après décrivent les différents types de nœuds utilisés avec HDInsight.

> [!NOTE]  
> Les types de nœuds utilisables varient selon le type de cluster concerné. Par exemple, un type de cluster Hadoop ne comporte aucun nœud Nimbus. Pour plus d’informations sur les nœuds utilisés par les types de clusters HDInsight, consultez la section Types de cluster du document [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

### <a name="head-nodes"></a>Nœuds principaux

Pour garantir une haute disponibilité des services Hadoop, HDInsight fournit deux nœuds principaux. Les deux nœuds principaux sont actifs et s’exécutent simultanément sur le cluster HDInsight. Certains services, par exemple Apache HDFS et Apache Hadoop YARN, ne sont « actifs » que sur un seul nœud principal à un moment donné. D'autres services tels que HiveServer2 ou Hive MetaStore sont actifs sur les deux nœuds principaux simultanément.

Pour obtenir les noms d’hôte de différents types de nœuds dans votre cluster, veuillez utiliser [l’API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> N’associez pas la valeur numérique avec l’information selon laquelle un nœud est principal ou secondaire. La valeur numérique est uniquement présente afin de fournir un nom unique à chaque nœud.

### <a name="nimbus-nodes"></a>Nœuds Nimbus

Les nœuds Nimbus sont disponibles avec les clusters Apache Storm. Les nœuds Nimbus offrent des fonctionnalités comparables au service Hadoop JobTracker en distribuant et en surveillant le traitement sur l’ensemble des nœuds de travail. HDInsight fournit deux nœuds Nimbus pour les clusters Storm

### <a name="apache-zookeeper-nodes"></a>Nœuds Apache ZooKeeper

Des nœuds [ZooKeeper](https://zookeeper.apache.org/) sont utilisés pour l’élection de leader des services maîtres sur nœuds principaux. Ils permettent également de garantir que ces services, nœuds de données (worker) et passerelles savent sur quel nœud principal un service maître est actif. Par défaut, HDInsight fournit trois nœuds ZooKeeper.

### <a name="worker-nodes"></a>Nœuds de travail

Les nœuds de travail effectuent l’analyse de données proprement dite lorsqu’un travail est soumis au cluster. En cas de défaillance d’un nœud de travail, la tâche en cours d’exécution sur ce dernier est soumise à un autre nœud de travail. Par défaut, HDInsight crée quatre nœuds de travail. Vous pouvez modifier ce chiffre en fonction vos besoins, pendant et après la création du cluster.

### <a name="edge-node"></a>Nœud de périphérie

Un nœud de périphérie ne participe pas activement à l’analyse de données au sein du cluster. Il est utilisé par les développeurs ou scientifiques des données qui travaillent avec Hadoop. Le nœud de périmètre se trouve dans le même réseau virtuel Azure que les autres nœuds du cluster et peut accéder directement à tous les autres nœuds. Le nœud Edge peut être utilisé sans qu’il faille recourir à des ressources de services Hadoop ou de tâches d’analyse critiques.

Pour l’heure, ML Services sur HDInsight est le seul type de cluster à proposer un nœud de périphérie par défaut. Dans le cas de ML Services sur HDInsight, le nœud de périphérie est utilisé pour tester le code R localement sur le nœud avant de l’envoyer au cluster à des fins de traitement distribué.

Pour plus d’informations sur l’utilisation d’un nœud de périphérie avec d’autres types de cluster, consultez le document [Utiliser des nœuds de périphérie dans HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="accessing-the-nodes"></a>Accès aux nœuds

L’accès au cluster par Internet est fourni par le biais d’une passerelle publique. L’accès est limité à la connexion aux nœuds principaux et (s’il en existe un) au nœud de périphérie. L’accès aux services exécutés sur les nœuds principaux n’est pas affecté par la présence de plusieurs nœuds principaux. La passerelle publique achemine les demandes vers le nœud principal qui héberge le service demandé. Par exemple, si Apache Ambari est hébergé sur le nœud principal secondaire, la passerelle route les demandes entrantes pour Ambari vers ce nœud.

L’accès via la passerelle publique est limité aux ports 443 (HTTPS), 22 et 23.

|Port |Description |
|---|---|
|443|Il permet d’accéder à Ambari et à d’autres interfaces utilisateur web ou API REST hébergées sur les nœuds principaux.|
|22|Il est utilisé pour accéder au nœud principal primaire ou au nœud de périphérie via SSH.|
|23|Il est utilisé pour accéder au nœud principal secondaire via SSH. Par exemple, `ssh username@mycluster-ssh.azurehdinsight.net` établit une connexion au nœud principal primaire du cluster nommé **mycluster**.|

Pour plus d’informations sur l’utilisation de SSH, consultez le document [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Noms de domaine pleinement qualifiés internes (FQDN)

Les nœuds présents dans un cluster HDInsight sont dotés d’une adresse IP interne et d’un nom de domaine complet uniquement accessibles à partir du cluster. Lorsque vous accédez à des services sur le cluster à l'aide de l'adresse IP ou du nom de domaine complet interne, vous devez utiliser Ambari pour vérifier l'adresse IP ou le nom de domaine complet à utiliser.

Par exemple, le service Apache Oozie peut s’exécuter uniquement sur un nœud principal, et l’utilisation de la commande `oozie` à partir d’une session SSH nécessite l’URL du service. Cette URL peut être extraite d’Ambari avec la commande suivante :

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Cette commande renvoie une valeur similaire à la valeur suivante, qui contient l’URL interne à utiliser avec la commande `oozie` :

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Pour plus d’informations sur l’API REST Ambari, consultez [Surveiller et gérer HDInsight avec l’API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Accès à d’autres types de nœuds

Vous pouvez vous connecter aux nœuds qui ne sont pas directement accessibles sur Internet en utilisant les méthodes suivantes :

|Méthode |Description |
|---|---|
|SSH|une fois connecté à un nœud principal au moyen de SSH, vous pouvez utiliser SSH à partir de ce nœud principal pour vous connecter à d’autres nœuds du cluster. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Tunnel SSH|Si vous avez besoin d’accéder à un service web hébergé sur l’un des nœuds qui n’est pas exposé à Internet, vous devez utiliser un tunnel SSH. Pour plus d’informations, consultez le document [Utiliser un tunnel SSH avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).|
|Réseau virtuel Azure|si votre cluster HDInsight fait partie intégrante d’un réseau virtuel Azure, toutes les ressources du même réseau virtuel peuvent accéder directement à tous les nœuds du cluster. Pour plus d’informations, consultez le document [Planifier un réseau virtuel pour HDInsight](hdinsight-plan-virtual-network-deployment.md).|

## <a name="how-to-check-on-a-service-status"></a>Comment contrôler  l'état d'un service

Pour vérifier l’état des services qui s’exécutent sur les nœuds principaux, utilisez l’interface utilisateur web d’Ambari ou l’API REST Ambari.

### <a name="ambari-web-ui"></a>Interface utilisateur web d'Ambari

L’interface utilisateur web d’Ambari est visible à l’adresse `https://CLUSTERNAME.azurehdinsight.net`. Remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, saisissez les informations d'identification utilisateur de votre cluster. Le nom d'utilisateur HTTP par défaut est **admin** et le mot de passe est le mot de passe que vous avez saisi lors de la création du cluster.

Lorsque vous arrivez sur la page Ambari, les services installés apparaissent à gauche de la page.

![Apache Ambari - Services installés](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Une série d'icônes s'affichent en regard d'un service pour indiquer son état. Des alertes liées à un service peuvent être affichées à l'aide du lien **Alertes** situé en haut de la page.  Ambari offre plusieurs alertes prédéfinies.

Les alertes suivantes aident à superviser la disponibilité d’un cluster :

| Nom de l’alerte                               | Description                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Metric Monitor Status                    | Cette alerte indique l’état du processus de supervision des métriques, tel que déterminé par le script d’état de la supervision.                                                                                   |
| Ambari Agent Heartbeat                   | Cette alerte est déclenchée si le serveur a perdu le contact avec un agent.                                                                                                                        |
| ZooKeeper Server Process                 | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le processus du serveur ZooKeeper est opérationnel et à l’écoute sur le réseau.                                                               |
| IOCache Metadata Server Status           | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le serveur de métadonnées IOCache est opérationnel et répond aux demandes des clients.                                                            |
| JournalNode Web UI                       | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web de JournalNode est inaccessible.                                                                                                                 |
| Spark2 Thrift Server                     | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le serveur Thrift Spark2 est opérationnel.                                                                                                |
| History Server Process                   | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le processus du serveur d’historique est opérationnel et à l’écoute sur le réseau.                                                                |
| History Server Web UI                    | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web du serveur d’historique est inaccessible.                                                                                                              |
| Interface utilisateur web `ResourceManager`                   | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web `ResourceManager` est inaccessible.                                                                                                             |
| NodeManager Health Summary               | Cette alerte au niveau du service est déclenchée si des NodeManagers ne sont pas sains                                                                                                                    |
| App Timeline Web UI                      | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web de chronologie d’application est inaccessible.                                                                                                         |
| DataNode Health Summary (Récapitulatif d’intégrité DataNode)                  | Cette alerte de niveau de service est déclenchée si des DataNodes ne sont pas sains                                                                                                                       |
| NameNode Web UI                          | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web de NameNode est inaccessible.                                                                                                                    |
| ZooKeeper Failover Controller Process    | Cette alerte au niveau de l’hôte est déclenchée s’il n’est pas possible de confirmer que le processus du contrôleur de basculement ZooKeeper est opérationnel et à l’écoute sur le réseau.                                                   |
| Oozie Server Web UI                      | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web du serveur Oozie est inaccessible.                                                                                                                |
| Oozie Server Status                      | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le serveur Oozie est opérationnel et répond aux demandes des clients.                                                                      |
| Hive Metastore Process                   | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le processus Hive Metastore est opérationnel et à l’écoute sur le réseau.                                                                 |
| HiveServer2 Process                      | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le serveur HiveServer est opérationnel et répond aux demandes des clients.                                                                        |
| WebHCat Server Status                    | Cette alerte au niveau de l’hôte est déclenchée si l’état du serveur `templeton` n’est pas sain.                                                                                                            |
| Percent ZooKeeper Servers Available      | Cette alerte est déclenchée si le nombre de serveurs ZooKeeper en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications des processus ZooKeeper.     |
| Spark2 Livy Server                       | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le serveur Livy2 est opérationnel.                                                                                                        |
| Spark2 History Server                    | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le serveur d’historique Spark2 est opérationnel.                                                                                               |
| Metrics Collector Process                | Cette alerte est déclenchée s’il n’est pas possible de confirmer que le collecteur d’indicateurs de performance est opérationnel et à l’écoute sur le port configuré, pendant un nombre de secondes égal au seuil.                                 |
| Metrics Collector - HBase Master Process | Cette alerte est déclenchée s’il n’est pas possible de confirmer que les processus maîtres HBase du collecteur d’indicateurs de performance sont opérationnels et à l’écoute sur le réseau pour le seuil critique configuré, exprimé en secondes. |
| Percent Metrics Monitors Available       | Cette alerte est déclenchée si un pourcentage des processus de supervision des indicateurs de performance ne sont pas opérationnels et à l’écoute sur le réseau pour les seuils critiques et d’avertissement configurés.                             |
| Percent NodeManagers Available           | Cette alerte est déclenchée si le nombre de NodeManagers en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications des processus NodeManager.        |
| NodeManager Health                       | Cette alerte au niveau de l’hôte vérifie la propriété de contrôle d’intégrité du nœud disponible à partir du composant de NodeManager.                                                                                              |
| NodeManager Web UI                       | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web de NodeManager est inaccessible.                                                                                                                 |
| NameNode High Availability Health (Intégrité de la haute disponibilité de NameNode)        | Cette alerte de niveau de service est déclenchée si Active NameNode ou Standby NameNode ne sont pas exécutés.                                                                                     |
| DataNode Process                         | Cette alerte au niveau de l’hôte est déclenchée s’il n’est pas possible d’établir que les processus DataNode individuels sont opérationnels et à l’écoute sur le réseau.                                                         |
| DataNode Web UI                          | Cette alerte au niveau de l’hôte est déclenchée si l’interface utilisateur web de DataNode est inaccessible.                                                                                                                    |
| Percent JournalNodes Available (Pourcentage de JournalNodes disponibles)           | Cette alerte est déclenchée si le nombre de JournalNodes en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications de processus JournalNode.        |
| Percent DataNodes Available (Pourcentage de DataNodes disponibles)              | Cette alerte est déclenchée si le nombre de DataNodes en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications de processus DataNode.              |
| Zeppelin Server Status                   | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si le serveur Zeppelin est opérationnel et répond aux demandes des clients.                                                                   |
| HiveServer2 Interactive Process          | Cette alerte au niveau de l’hôte est déclenchée lorsqu’il n’est pas possible de déterminer si HiveServerInteractive est opérationnel et répond aux demandes des clients.                                                             |
| LLAP Application                         | Cette alerte est déclenchée lorsqu’il n’est pas possible de déterminer si l’application LLAP est opérationnelle et répond aux demandes.                                                                                    |

Vous pouvez sélectionner chaque service pour afficher plus d'informations sur ce dernier.

La page de service fournit des informations sur l’état et la configuration de chaque service. Il ne fournit pas d’informations sur le nœud principal sur lequel le service s’exécute. Pour afficher ces informations, utilisez le lien **Hôtes** en haut de la page. Cette page affiche les hôtes au sein du cluster, notamment les nœuds principaux.

![Apache Ambari - Liste des hôtes, nœuds principaux](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Sélectionner le lien de l’un des nœuds principaux permet d’afficher les services et les composants qui s’exécutent sur ce nœud.

![Apache Ambari - État des composants](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Pour plus d’informations sur l’utilisation d’Ambari, consultez [Surveiller et gérer HDInsight avec l’interface utilisateur web d’Apache Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>API Ambari REST

L’API REST Ambari est disponible sur internet. La passerelle publique HDInsight gère l’acheminement des demandes vers le nœud principal hébergeant l’API REST.

Vous pouvez utiliser la commande suivante pour vérifier l'état d'un service via l'API REST Ambari :

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Remplacez **PASSWORD** par le mot de passe du compte d’utilisateur HTTP (admin).
* Remplacez **CLUSTERNAME** par le nom de votre cluster.
* Remplacez **SERVICENAME** par le nom du service dont vous voulez contrôler l’état.

Par exemple, pour vérifier l’état du service **HDFS** dans un cluster nommé **mycluster**, avec un mot de passe **password**, vous devez utiliser la commande suivante :

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

La réponse se présente comme le JSON suivant :

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

L’URL nous indique que le service est en cours d’exécution sur un nœud principal nommé **mycluster.wutj3h4ic1zejluqhxzvckxq0g**.

L'URL indique que le service est en cours d'exécution ou **démarré**.

Si vous ne connaissez pas les services installés sur le cluster, vous pouvez utiliser la commande suivante pour en récupérer la liste :

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Pour plus d’informations sur l’API REST Ambari, consultez [Surveiller et gérer HDInsight avec l’API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Composants du service

Les services peuvent contenir des composants dont vous souhaitez vérifier l'état individuellement. Par exemple, HDFS contient le composant NameNode. Pour afficher des informations relatives à un composant, la commande serait :

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Si vous ne connaissez pas les composants fournis par un service, vous pouvez utiliser la commande suivante pour en récupérer la liste :

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Accès aux fichiers journaux sur les nœuds principaux

### <a name="ssh"></a>SSH

Lorsque vous êtes connecté à un nœud principal via SSH, les fichiers journaux se trouvent sous **/var/log**. Par exemple, **/var/log/hadoop-yarn/yarn** contiennent les journaux d’activité correspondant à YARN.

Chaque nœud principal peut contenir des entrées de journal d’activité uniques. Vous devez donc vérifier les journaux d’activité correspondant aux deux.

### <a name="sftp"></a>SFTP

Vous pouvez également vous connecter au nœud principal à l’aide du protocole FTP SSH, ou protocole FTP sécurisé (SFTP), et télécharger directement les fichiers journaux.

Comme lors de l’utilisation d’un client SSH, lorsque vous vous connectez au cluster, vous devez fournir le nom du compte d’utilisateur SSH et l’adresse SSH du cluster. Par exemple : `sftp username@mycluster-ssh.azurehdinsight.net`. Lorsque vous y êtes invité, fournissez le mot de passe du compte ou une clé publique à l’aide du paramètre `-i`.

Une fois connecté, vous voyez apparaître une invite `sftp>`. À partir de cette invite, vous pouvez changer de répertoire, ainsi que charger et télécharger des fichiers. Par exemple, les commandes ci-après activent le répertoire **/var/log/hadoop/hdfs** , puis téléchargent tous les fichiers dans ce répertoire.

    cd /var/log/hadoop/hdfs
    get *

Pour obtenir la liste des commandes disponibles, entrez `help` au niveau de l’invite `sftp>`.

> [!NOTE]  
> Il existe également des interfaces graphiques qui vous permettent de visualiser le système de fichiers lorsque vous êtes connecté à l’aide du protocole SFTP. Par exemple, [MobaXTerm](https://mobaxterm.mobatek.net/) vous offre la possibilité de parcourir le système de fichiers au moyen d’une interface semblable à l’Explorateur Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Pour accéder aux fichiers journaux avec Ambari, vous devez utiliser un tunnel SSH. Les interfaces web des différents services ne sont pas exposée publiquement sur Internet. Pour plus d’informations sur l’utilisation du tunnel SSH, voir le document [Utilisation d’un Tunneling SSH](hdinsight-linux-ambari-ssh-tunnel.md).

Dans l’interface utilisateur web d’Ambari, sélectionnez le service dont vous souhaitez afficher les journaux d’activité (par exemple, YARN). Utilisez ensuite les **liens rapides** pour sélectionner le nœud principal pour lequel vous souhaitez afficher les journaux d’activité.

![Utilisation des liens rapides pour afficher les journaux d’activité](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Configuration de la taille des nœuds

La taille d’un nœud n’est sélectionnable que lors de la création du cluster. Pour obtenir la liste des différentes tailles de machine virtuelle disponibles pour HDInsight, voir la page [Tarification de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Lorsque vous créez un cluster, vous pouvez spécifier la taille des nœuds. Les informations suivantes aident à déterminer comment spécifier la taille avec le [portail Azure](https://portal.azure.com/), le [module Azure PowerShell Az](/powershell/azureps-cmdlets-docs) et [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) :

* **Portail Azure**: lors de la création d’un cluster, vous pouvez définir la taille des nœuds utilisés par le cluster :

    ![Image de l'Assistant de création de cluster avec sélection de taille de nœud](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI** : lorsque vous utilisez la commande [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create), vous pouvez définir la taille des nœuds principaux, worker et ZooKeeper en utilisant les paramètres `--headnode-size`, `--workernode-size` et `--zookeepernode-size`.

* **Azure PowerShell**: Quand vous utilisez l’applet de commande [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster), vous pouvez définir la taille des nœuds principaux, worker et ZooKeeper en utilisant les paramètres `-HeadNodeSize`, `-WorkerNodeSize` et `-ZookeeperNodeSize`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités présentées dans cet article, voir :

* [Informations de référence sur REST Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installation et configuration Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Installer et configurer le module Azure PowerShell Az](/powershell/azure/overview)
* [Gestion de HDInsight avec Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Approvisionnement de clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md)
